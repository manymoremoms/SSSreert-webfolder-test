REM
REM @title               htmlZoneMessage
REM @author              Sean zhang
REM @date-created        04/28/2025
REM @date-last-modified  04/28/2025
REM @minimum-FW          1.0.0
REM
REM @description         This plugin allows you to send and receive zone messages from HTML.
REM
REM
Function htmlZoneMessage_Initialize(msgPort As Object, userVariables As Object, bsp As Object)
  print "htmlZoneMessage_Initialize - entry"
  htmlZoneMessage = htmlZoneMessage_New(msgPort, userVariables, bsp)
  bsp.SetPowerSaveMode(false)
  return htmlZoneMessage
End Function

Function htmlZoneMessage_New(msgPort As Object, userVariables As Object, bsp As Object)
  print "htmlZoneMessage_New - entry"
  s = {}
  s.version = 1.0
  s.msgPort = msgPort
  s.bsp = bsp
  s.userVariables = userVariables
  s.ProcessEvent = htmlZoneMessage_ProcessEvent
  s.objectName = "htmlZoneMessage_object"
  s.htmlWidget = FindHTMLWidget(bsp)

  return s
End Function

Function htmlZoneMessage_ProcessEvent(event As Object) As boolean
  'Receive a plugin message
      ? "Event Received: "
      ? event
      if (type(m.htmlWidget) <> "roHTMLWidget")
        m.htmlWidget = findHTMLWidget(m.bsp)
      endif
      if type(event) = "roAssociativeArray" then
        ? "EEEEVENT"
        print "EventType:";event["EventType"] 
        if type(event["EventType"]) = "roString"
          if event["EventType"] = "SEND_PLUGIN_MESSAGE" then
            if event["PluginName"] = "htmlZoneMessage" then
              pluginMessage$ = event["PluginMessage"]
              print pluginMessage$
              'return true when you want no other event processors to handle this event
              m.htmlwidget.postJSMessage({PluginMessage:event["PluginMessage"]})                       
            endif
          else if event["EventType"] = "SEND_ZONE_MESSAGE" then
            'stop
            'm.htmlwidget.postJSMessage({zoneMessage:event["EventParameter"]})
            print "SEND_ZONE_MESSAGE:";event["EventParameter"]
            return false
          endif
        endif
      else if type(event) = "roHtmlWidgetEvent" then
        ? "WIDGET EVENT"
        eventData = event.getData()
        print eventData
        if type(eventData) = "roAssociativeArray" and type(eventData.reason) = "roString" then
          if eventData["reason"] = "message" then
            ? "MESSAGE"    
            print eventData.message           
            if type(eventData.message.htmlready) = "roString"
              ? "HTML Ready"
            else if type(eventData.message.tempBrightness) = "roString"
              tempBrightness = eventData.message.tempBrightness
              print "tempBrightness:"; tempBrightness
              setBrightness(m.bsp, tempBrightness)
            else if type(eventData.message.brightness) = "roString"
              brightness = eventData.message.brightness
              print "brightness:"; brightness
              setBrightness(m.bsp, brightness)
              UpdateUserVariable(m.userVariables, m.msgPort, m.bsp, "brightness",brightness)
            else if type(eventData.message.tempVolume) = "roString"
              tempVolume = eventData.message.tempVolume
              print "tempVolume:"; tempVolume
              setPlayerVolume(tempVolume)
            else if type(eventData.message.volume) = "roString"
              volume = eventData.message.volume
              print "volume:"; volume
              setPlayerVolume(volume)
              UpdateUserVariable(m.userVariables, m.msgPort, m.bsp, "volume",volume)
            else if type(eventData.message.tvPowerState) = "roString"
              tvPowerState = eventData.message.tvPowerState
              print "tvPowerState:"; tvPowerState
              setTvPowerState(m.bsp, tvPowerState)
              UpdateUserVariable(m.userVariables, m.msgPort, m.bsp, "tvPowerState",tvPowerState)
            else if type(eventData.message.rebootFlag) = "roString"
              rebootFlag = eventData.message.rebootFlag
              print "rebootFlag:"; rebootFlag
              UpdateUserVariable(m.userVariables, m.msgPort, m.bsp, "rebootFlag",rebootFlag)
            else if type(eventData.message.automaticOn) = "roString"
              automaticOn = eventData.message.automaticOn
              print "automaticOn:"; automaticOn
              UpdateUserVariable(m.userVariables, m.msgPort, m.bsp, "automaticOn",automaticOn)
            else if type(eventData.message.defaultVolume) = "roString"
              defaultVolume = eventData.message.defaultVolume
              print "defaultVolume:"; defaultVolume
              UpdateUserVariable(m.userVariables, m.msgPort, m.bsp, "defaultVolume",defaultVolume)
            else if type(eventData.message.defaultBrightness) = "roString"
              defaultBrightness = eventData.message.defaultBrightness
              print "defaultBrightness:"; defaultBrightness
              setBrightness(m.bsp, defaultBrightness)
              UpdateUserVariable(m.userVariables, m.msgPort, m.bsp, "defaultBrightness",defaultBrightness)
            else if type(eventData.message.getUserVariable) = "roString"
              vName = eventData.message.getUserVariable
              vValue = lynxGetUserVariable(m.userVariables,vName)
              message = CreateObject("roAssociativeArray")
              message[vName] = vValue
              messageStr = FormatJson(message)
              print "getUserVariable:";messageStr
              m.htmlwidget.postJSMessage({PluginMessage:messageStr})
            else if type(eventData.message.ScheduleJson) = "roString"
              ScheduleJson = eventData.message.ScheduleJson
              print "ScheduleJson:"; ScheduleJson
              UpdateUserVariable(m.userVariables, m.msgPort, m.bsp, "ScheduleJson",ScheduleJson)
            else if type(eventData.message.SetVideoPlayerState) = "roString"
              state = eventData.message.SetVideoPlayerState
              print "SetVideoPlayerState:"; state
              setPlayerState(m.bsp,state)
            else if type(eventData.message.startbackdoor) = "roString"
              startbackdoorFlag = eventData.message.startbackdoor
              print "startbackdoorFlag:"; startbackdoorFlag
              if startbackdoorFlag = "1" then
                message = "startbackdoor"
                lynxSendZoneMessage(m.bsp,message)
              else
                print "Nnknow command"
              endif              
            else if type(eventData.message.setDeviceCommand) = "roString"
              cmd = eventData.message.setDeviceCommand
              print "setDeviceCommand:"; cmd
              if cmd = "setDisplayReboot" then
                SafeReboot()
              else
                print "Nnknow command"
              endif
            else if type(eventData.message.getDateTime) = "roString"
              systemTime = CreateObject("roSystemTime")
              currentDateTime = systemTime.GetLocalDateTime()
              systemTime = invalid
              message = CreateObject("roAssociativeArray")
              print "currentDateTime:";currentDateTime.GetString()
              message["getDateTime"] = currentDateTime.GetString()
              messageStr = FormatJson(message)
              m.htmlwidget.postJSMessage({PluginMessage:messageStr})
            else if type(eventData.message.geTimeForSchedule) = "roString"
              systemTime = CreateObject("roSystemTime")
              currentDateTime = systemTime.GetLocalDateTime()
              systemTime = invalid
              message = CreateObject("roAssociativeArray")
              print "currentDateTime:";currentDateTime.GetString()
              message["geTimeForSchedule"] = currentDateTime.GetString()
              messageStr = FormatJson(message)
              m.htmlwidget.postJSMessage({PluginMessage:messageStr})
            else if type(eventData.message.geTimeForPowerOn) = "roString"
              systemTime = CreateObject("roSystemTime")
              currentDateTime = systemTime.GetLocalDateTime()
              systemTime = invalid
              message = CreateObject("roAssociativeArray")
              print "currentDateTime:";currentDateTime.GetString()
              message["geTimeForPowerOn"] = currentDateTime.GetString()
              messageStr = FormatJson(message)
              m.htmlwidget.postJSMessage({PluginMessage:messageStr})
            else if type(eventData.message.setSystemDateTime) = "roString"
              print "setSystemDateTime:";eventData.message.setSystemDateTime
              result = SetSystemTime(eventData.message.setSystemDateTime)
              if (result) then
                print "SetSystemTime success"
              endif
            else if type(eventData.message.heartbeat) = "roString" and eventData.message.heartbeat = "alive" then
              print "Received heartbeat - resetting idle timer"
              lynxSendTouchEvent(m.msgPort)
            endif
          else if eventData["reason"] = "load-finished" then
            ? "reason:load-finished" 
            bri = lynxGetUserVariable(m.userVariables,"brightness")
            vol = lynxGetUserVariable(m.userVariables,"volume")
            tvState = lynxGetUserVariable(m.userVariables,"tvPowerState")
            rebootFlag = lynxGetUserVariable(m.userVariables,"rebootFlag")
            automaticOn = lynxGetUserVariable(m.userVariables,"automaticOn")
            defaultVolume = lynxGetUserVariable(m.userVariables,"defaultVolume")
            defaultBrightness = lynxGetUserVariable(m.userVariables,"defaultBrightness")
            ScheduleJson = lynxGetUserVariable(m.userVariables,"ScheduleJson")
            myData = CreateObject("roAssociativeArray")
            myData["brightness"] = bri
            myData["tvPowerState"] = tvState
            myData["rebootFlag"] = rebootFlag
            myData["automaticOn"] = automaticOn
            myData["defaultVolume"] = defaultVolume
            if rebootFlag = "1" then
              print "reboot,dont change the volume."
            else
              myData["volume"] = defaultVolume
            endif
            myData["defaultBrightness"] = defaultBrightness
            myData["ScheduleJson"] = ScheduleJson
            setPlayerVolume(defaultVolume)
            setBrightness(m.bsp, defaultBrightness)
            UpdateUserVariable(m.userVariables, m.msgPort, m.bsp, "volume",defaultVolume)
            print myData
            myDataStr = FormatJson(myData)
            m.htmlwidget.postJSMessage({PluginMessage:myDataStr})
          endif
        endif
      endif



      'Send a plugin message to other plugins and BrightAuthor Objects
      if (false)
        pluginMessageCmd = {
          EventType    : "EVENT_PLUGIN_MESSAGE",
          PluginName   : "htmlZoneMessage",
          PluginMessage: "empty"
        }
        m.msgPort.PostMessage(pluginMessageCmd)
      endif

      'return false if another plugin or event processor should get a chance to handle this event
      return false

End Function

Function FindHTMLWidget(bsp)
    for each baZone in bsp.sign.zonesHSM
        if baZone.loadingHtmlWidget <> invalid then
            return baZone.loadingHtmlWidget
        end if
    end for
    print "Couldn't find htmlwidget"
    return false
End Function

Function setBrightness(bsp As Object, brightness as String)
  print brightness
  serialMessage = "set.brightness." + brightness
  SendSerialMessage(bsp,serialMessage)
  return true
End Function

Function setPlayerVolume(volume As String) 
  print "setPlayerVolume:";volume
  newVolume% = int(val(volume))  
  audioOutput = CreateObject("roAudioOutput", GetAudioOutputConnector("HDMI"))
  if type(audioOutput) = "roAudioOutput" then
    if newVolume% > 100 then
      newVolume% = 100
    else if newVolume% < 0
      newVolume% = 0
    end if
    audioOutput.SetVolume(newVolume%)
  end if
  
  return newVolume%
End Function

Function setTvPowerState(bsp As Object, state as String)
  serialMessage = ""
  if (state = "1") then
    print "powerOn"
    serialMessage = "set.tv.on"
  else if (state = "0") then
    print "powerOff"
    serialMessage = "set.tv.off"
  endif
  SendSerialMessage(bsp,serialMessage)
  return true
End Function

Function UpdateUserVariable( userVariables As Object, msgPort As Object, bsp As Object, varName As String, varValue As Dynamic) As Boolean
    if userVariables = invalid then
        print "userVariables error."
        return false
    end if
    
    if not userVariables.DoesExist(varName) then
        print varName; " can't find."
    end if

    userVariable = userVariables.Lookup(varName)
    userVariable.SetCurrentValue(varValue, false)
    userVariablesUpdated = true

    if userVariablesUpdated then
      userVariablesChanged = CreateObject("roAssociativeArray")
      userVariablesChanged["EventType"] = "USER_VARIABLES_UPDATED"
      msgPort.PostMessage(userVariablesChanged)

      ' Notify controlling devices to refresh
      bsp.SendUDPNotification("refresh")
    endif
    return true 
End Function

Function lynxGetUserVariable(userVariables As Object, varName As String)
  if userVariables = invalid then
      print "userVariables error."
      return false
  end if
    
  if not userVariables.DoesExist(varName) then
      print varName; " can't find."
  end if

  userVariable = userVariables.Lookup(varName)
  value = userVariable.GetCurrentValue()
  return value
End Function

Function SendSerialMessage(bsp As Object, message as String) as Boolean
  port$ = "2"
  serialString$ = message
  print "serialString:";serialString$
  if type(bsp.serial) = "roAssociativeArray" then
    serial = bsp.serial[port$]
    if type(serial) = "roSerialPort" or type(serial) = "roUsbTap" then
      serial.SendLine(serialString$)
    end if
  end if

  return true
End Function

function setPlayerState(bsp As Object, state as String)
  result = false
  for each zoneHSM in bsp.sign.zonesHSM
    if type(zoneHSM.videoPlayer) = "roVideoPlayer" then
      if state = "Play" then
        zoneHSM.videoPlayer.Play()
        result = true
      else if state = "Pause" then
        zoneHSM.videoPlayer.Pause()
        result = true
      else if state = "Stop" then
        zoneHSM.videoPlayer.Stop()
        result = true
      end if 
    end if
  next
  return result
end function

function SetSystemTime(isoTimeString as String) as Boolean
  print "isoTimeString:";isoTimeString
  parsedDateTime = ParseDateTime(isoTimeString)
  if parsedDateTime <> invalid then
    print "roDateTime:";parsedDateTime
    sysTime = CreateObject("roSystemTime")
    return sysTime.SetLocalDateTime(parsedDateTime)
  else
    return false
  end if
end function

function ParseDateTime(isoString as String) as Object
  dateTime = CreateObject("roDateTime")
  
  regex = CreateObject("roRegEx", "^(\d{4})-(\d{2})-(\d{2})T(\d{2}):(\d{2}):(\d{2})\.(\d{3})$", "")
  match = regex.Match(isoString)
  
  if match.Count() = 8 then
    year = Val(match[1])
    month = Val(match[2])
    day = Val(match[3])
    hour = Val(match[4])
    minute = Val(match[5])
    second = Val(match[6])
    ms = Val(match[7])
    
    dateTime.SetYear(year)
    dateTime.SetMonth(month)
    dateTime.SetDay(day)
    dateTime.SetHour(hour)
    dateTime.SetMinute(minute)
    dateTime.SetSecond(second)
    dateTime.SetMillisecond(ms)
    return dateTime
  end if
  
  ' 全部失败则返回无效对象
  return invalid
end function

function SafeReboot()
    ' 检查当前播放器是否支持重启
    deviceInfo = createObject("roDeviceInfo")
    if deviceInfo.canReboot() then
        print "设备支持重启，正在执行..."
        rebootSystem()
    else
        print "此设备不支持软件重启"
    end if
end function

Function lynxSendZoneMessage(bsp As Object, message as String)
  print message
  zoneMessageCmd = { }
  zoneMessageCmd["EventType"] = "SEND_ZONE_MESSAGE"
  zoneMessageCmd["EventParameter"] = message
  bsp.msgPort.PostMessage(zoneMessageCmd)
  return true
End Function

Function lynxSendTouchEvent(msgPort As Object)
  touchEvent = CreateObject("roAssociativeArray")
  touchEvent["EventType"] = "LIGHT_TOUCH"
  touchEvent["touch_x"] = 10
  touchEvent["touch_y"] = 10
  msgPort.PostMessage(touchEvent)
  return true
End Function
