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

