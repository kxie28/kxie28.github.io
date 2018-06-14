Phantom Playbooks
=================

These are some of the playbooks that I put together to run in Phantom for automation purposes

Microsoft ATA Runbook Playbook
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
::
        
        parameters = []
        artifact_name = ""

        for artifacts in check_array:
        art, result = phantom.condition(
                container=container,
                conditions=[["artifact:*.name", "==", artifacts],],)
        if art or result:
                parameters.append({
                'message': artifacts+' event detected',
                'destination': "phantom",
                })
                if art or result:
                artifact_name += artifacts

        #convert the startTime string into something useable
        from datetime import datetime
        datapaths = ['artifact:*.data.startTime']
        
        #collect the startTime artifact
        data = phantom.collect(container=container,
                                datapath=datapaths,
                                scope='all',
                                limit=100,
                                none_if_first=False)
        phantom.debug(data)
        time = ""
        timedate = ""
        #Since the artifact collected is a list of lists, need to go through and extract just the string
        for i in data:
                phantom.debug(i)
                for j in i:
                        phantom.debug(j)
                        time += j
                        timedate += j
        
        #Format the string into the time format needed for checks
        tformat = "%Y-%m-%d %H:%M:%S.%f"
        dt = datetime.strptime(time, tformat)
        the_time = dt.time()
        phantom.debug(the_time)
        check_time_mes, check_time_res = phantom.condition(
                container=container,
                conditions=[[str(the_time), ">=", "05:00:000"],
                        [str(the_time), "<=", "17:00:000"],],
                logical_operator='and',
                name="filter_1:condition_1")
                
        if check_time_mes or check_time_res:        
                phantom.act("send message", parameters=parameters, assets=['slacktest'], name="send_message")

        check_time_mes1, check_time_res1 = phantom.condition(
                container=container,
                conditions=[[str(the_time), ">", "17:00:000"],
                        [str(the_time), "<", "5:00:000"],],
                logical_operator='and',
                name="filter_2:condition_2")
                
        #phantom.debug("this is a test\nI need a new line please\n...")
        #Check time, if off work time, send email
        if check_time_mes1 or check_time_res1:
                email_parameters = []
                priority = set(phantom.collect(container, 'artifact:*.data.priority'))
                #phantom.debug(priority)
                #priorities = []
                #check = []
                #for i in priority:
                #    check.append("priority" : i)
                #    for j in i:
                #        priorities += j
                #phantom.debug(check)
                #phantom.debug(priorities)
                deviceVendor = artifact_finding("artifact:*.data.deviceVendor")
                deviceProduct = artifact_finding("artifact:*.data.finalDeviceProduct")
                deviceEventClassId = artifact_finding("artifact:*.data.deviceEventClassId")
                deviceHostName = artifact_finding("artifact:*.data.deviceHostName")
                sourceUserName = artifact_finding("artifact:*.cef.sourceUserName")
                message = artifact_finding("artifact:*.data.message")
                hostName = artifact_finding("artifact:*.data.originalAgentHostName")
                
                for artifacts in email_array:
                        art, result = phantom.condition(
                                container=container,
                                conditions=[["artifact:*.name", "==", artifacts],],)
                        if art or result:
                                email_parameters.append({
                                'from': "phantom@intelgd.com",
                                'to': "xxx@xxxx.com",
                                'subject': "MS ATA Event: " + artifact_name,
                                #'body': artifacts + " attack from " + hostName,
                                'body': "Date in EST time zone: " + timedate +"\n" + "Event name/signature it flagged on: "+artifact_name + "\nDevice it's alerting on (FW, AV, etc.): " + deviceVendor + " " + deviceProduct + "\nDevice Event Class ID: " + deviceEventClassId + "\nDevice Host Name: " + deviceHostName + "\nAttacker User Name: " + sourceUserName + "\n\nMessage: " + message + "\n\nExecutive Summary\nOn " + timedate + ', the SIEM triggered a "'+ artifact_name + '" event.' ,
                    'attachments': "",
                })
        phantom.debug(artifact_name)
        phantom.debug(deviceVendor)
        phantom.debug(deviceProduct)
        phantom.act("send email", parameters=email_parameters, assets=['smtp_server'], name="send_email_1")
        
        return

        def on_finish(container, summary):
                phantom.debug('on_finish() called')
                # This function is called after all actions are completed.
                # summary of all the action and/or all detals of actions 
                # can be collected here.

        # summary_json = phantom.get_summary()
        # if 'result' in summary_json:
                # for action_result in summary_json['result']:
                # if 'action_run_id' in action_result:
                # action_results = phantom.get_action_results(action_run_id=action_result['action_run_id'], result_data=False, flatten=False)
                # phantom.debug(action_results)
        return


Investigation Playbook (IoCs)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::
        
        import phantom.rules as phantom
        import json
        from datetime import datetime, timedelta

        def on_start(container):
                phantom.debug('on_start() called')
                
                # call 'ip_reputation_1' block
                ip_reputation_1(container=container)
                
                # call 'domain_reputation_1' block
                domain_reputation_1(container=container)
                
                # call 'file_reputation_1' block
                file_reputation_1(container=container)
                
                return

        def ip_reputation_1(action=None, success=None, container=None, results=None, handle=None, filtered_artifacts=None, filtered_results=None):
            phantom.debug('ip_reputation_1() called')
            
            # collect data for 'ip_reputation_1' call
            container_data = phantom.collect2(container=container, datapath=['artifact:*.cef.destinationAddress', 'artifact:*.id'])
            
            parameters = []
            
            # build parameters list for 'ip_reputation_1' call
            for container_item in container_data:
                if container_item[0]:
                    parameters.append({
                        'ip': container_item[0],
                        # context (artifact id) is added to associate results with the artifact
                        'context': {'artifact_id': container_item[1]},
                    })
                    
            phantom.act("ip reputation", parameters=parameters, assets=['virustotal'], callback=decision_1, name="ip_reputation_1")    
            
            return

        def decision_1(action=None, success=None, container=None, results=None, handle=None, filtered_artifacts=None, filtered_results=None):   
            phantom.debug('decision_1() called')
            
            # check for 'if' condition 1
            matched_artifacts_1, matched_results_1 = phantom.condition(
                container=container,
                action_results=results,
                conditions=[
                    ["ip_reputation_1:action_result.summary.communicating_samples", ">", 0],
                ])
                
           # call connected blocks if condition 1 matched
            if matched_artifacts_1 or matched_results_1:
                join_format_1(action=action, success=success, container=container, results=results, handle=handle)
                return
                
            return

        def create_ticket_1(action=None, success=None, container=None, results=None, handle=None, filtered_artifacts=None, filtered_results=None):
            phantom.debug('create_ticket_1() called')
            
            #phantom.debug('Action: {0} {1}'.format(action['name'], ('SUCCEEDED' if success else 'FAILED')))
            
            # collect data for 'create_ticket_1' call
            formatted_data_1 = phantom.get_format_data(name='format_1')
            
            parameters = []
            
            # build parameters list for 'create_ticket_1' call
            parameters.append({
                'description': formatted_data_1,
                'fields': "",
                'project_key': "GDCCS",
                'summary': "IP Reputation Test",
                'priority': "Medium",
                'assignee': "",
                'vault_id': "",
                'issue_type': "Incident",
            })

            phantom.act("create ticket", parameters=parameters, assets=['jira'], name="create_ticket_1")    
            
            return

        def format_1(action=None, success=None, container=None, results=None, handle=None, filtered_artifacts=None, filtered_results=None):
            phantom.debug('format_1() called')
            
            template = """End Time: {0}
                        Event Name: {1}
                        Device Host Name: {2}
                        Source IP: {3}
                        Source Host Name: {4}
                        Source Port: {5}
                        Destination IP: {6}
                        Country: {7}
                        Destination Host Name: {8}"""
                        
           # parameter list for template variable replacement
            parameters = [
                        "ip_reputation_1:artifact:*.cef.endTime",
                        "ip_reputation_1:artifact:*.name",
                        "ip_reputation_1:artifact:*.cef.deviceHostname",
                        "ip_reputation_1:artifact:*.cef.sourceHostName",
                        "ip_reputation_1:artifact:*.cef.sourceHostName",
                        "ip_reputation_1:artifact:*.cef.sourcePort",
                        "ip_reputation_1:action_result.parameter.ip",
                        "ip_reputation_1:artifact:*.cef.destinationHostName",
                        "ip_reputation_1:artifact:*.cef.destinationPort",
            ]
            
            phantom.format(container=container, template=template, parameters=parameters, name="format_1")
            
            create_ticket_1(container=container)
            
            return

        def join_format_1(action=None, success=None, container=None, results=None, handle=None, filtered_artifacts=None, filtered_results=None):
            phantom.debug('join_format_1() called')

            # check if all connected incoming actions are done i.e. have succeeded or failed
            if phantom.actions_done([ 'ip_reputation_1', 'domain_reputation_1', 'file_reputation_1' ]):
        
                # call connected block "format_1"
                format_1(container=container, handle=handle)
                
            return

        def domain_reputation_1(action=None, success=None, container=None, results=None, handle=None, filtered_artifacts=None, filtered_results=None):
            phantom.debug('domain_reputation_1() called')

            # collect data for 'domain_reputation_1' call
            container_data = phantom.collect2(container=container, datapath=['artifact:*.cef.destinationDnsDomain', 'artifact:*.id'])

            parameters = []
            
            # build parameters list for 'domain_reputation_1' call
            for container_item in container_data:
                parameters.append({
                    'domain': container_item[0],
                    # context (artifact id) is added to associate results with the artifact
                    'context': {'artifact_id': container_item[1]},
                })
                    
            phantom.act("domain reputation", parameters=parameters, assets=['virustotal'], callback=join_format_1, name="domain_reputation_1")    
            
            return

        def file_reputation_1(action=None, success=None, container=None, results=None, handle=None, filtered_artifacts=None, filtered_results=None):
            phantom.debug('file_reputation_1() called')

            # collect data for 'file_reputation_1' call
            container_data = phantom.collect2(container=container, datapath=['artifact:*.cef.fileHash', 'artifact:*.id'])

            parameters = []
            
            # build parameters list for 'file_reputation_1' call
            for container_item in container_data:
                parameters.append({
                    'hash': container_item[0],
                    # context (artifact id) is added to associate results with the artifact
                    'context': {'artifact_id': container_item[1]},
                })
                
            phantom.act("file reputation", parameters=parameters, assets=['virustotal'], callback=join_format_1, name="file_reputation_1")    
            
            return

        def on_finish(container, summary):
            phantom.debug('on_finish() called')
            # This function is called after all actions are completed.
            # summary of all the action and/or all detals of actions 
            # can be collected here.

            # summary_json = phantom.get_summary()
            # if 'result' in summary_json:
                # for action_result in summary_json['result']:
                    # if 'action_run_id' in action_result:
                        # action_results = phantom.get_action_results(action_run_id=action_result['action_run_id'], result_data=False, flatten=False)
                        # phantom.debug(action_results)

            return
