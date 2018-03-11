# Node-RED-Ffmpeg-DVR
Live stream capture using FFMpeg segments in a ring buffer

```
[{"id":"eba846a3.c3ea48","type":"exec","z":"b517deb3.7318f","command":"ffmpeg","addpay":true,"append":"","useSpawn":"true","timer":"","oldrc":false,"name":"","x":650,"y":460,"wires":[[],["bb88112c.add25"],["b08fff24.2dca6","bb88112c.add25","829a73f.026fa9"]]},{"id":"bb88112c.add25","type":"switch","z":"b517deb3.7318f","name":"switch","property":"payload","propertyType":"msg","rules":[{"t":"cont","v":"frame=","vt":"str"},{"t":"cont","v":" Opening","vt":"str"},{"t":"cont","v":"Error","vt":"str"},{"t":"cont","v":"Invalid","vt":"str"}],"checkall":"true","repair":false,"outputs":4,"x":770,"y":460,"wires":[["d8c06de9.af761","11fdbe17.12b8d2"],["4b0253e5.dafa8c"],["b2b9a424.bc9e58"],["b2b9a424.bc9e58"]]},{"id":"d8c06de9.af761","type":"function","z":"b517deb3.7318f","name":"get time","func":"var i = msg.payload.indexOf(\"time=\");\nvar o =  msg.payload.substr(i + 5, 11);\nreturn {payload: o};","outputs":1,"noerr":0,"x":920,"y":440,"wires":[["51a2fa6.b4f2704"]]},{"id":"b08fff24.2dca6","type":"json","z":"b517deb3.7318f","name":"","property":"payload","action":"obj","pretty":false,"x":670,"y":560,"wires":[["14768c9e.771d83"]]},{"id":"51a2fa6.b4f2704","type":"ui_text","z":"b517deb3.7318f","group":"e996c6e3.977028","order":5,"width":0,"height":0,"name":"","label":"up time:","format":"{{msg.payload}}","layout":"row-spread","x":1060,"y":440,"wires":[]},{"id":"90aa707f.bc633","type":"function","z":"b517deb3.7318f","name":"cmd","func":"var s = \"-i \" + flow.get(\"input\");\ns += \" -vcodec copy -acodec copy -map 0\";\ns += \" -t \" + flow.get(\"total_time\");\ns += \" -f segment -strftime 1 -reset_timestamps 1 -segment_time \";\ns += Math.trunc(flow.get(\"total_frames\") / flow.get(\"fps\"));\ns += \" \" + flow.get(\"path\");\ns += flow.get(\"output\");\nreturn {payload: s};","outputs":1,"noerr":0,"x":650,"y":400,"wires":[["eba846a3.c3ea48"]]},{"id":"ac489b89.4c3eb8","type":"ui_text","z":"b517deb3.7318f","group":"e996c6e3.977028","order":7,"width":0,"height":0,"name":"rc","label":"return code:","format":"{{msg.payload}}","layout":"row-spread","x":910,"y":560,"wires":[]},{"id":"14768c9e.771d83","type":"change","z":"b517deb3.7318f","name":"code","rules":[{"t":"set","p":"payload","pt":"msg","to":"payload.code","tot":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":790,"y":560,"wires":[["ac489b89.4c3eb8"]]},{"id":"8783cd49.ddabd","type":"ui_button","z":"b517deb3.7318f","name":"","group":"4fc6d69.aab3928","order":7,"width":0,"height":0,"passthru":true,"label":"Start","color":"","bgcolor":"","icon":"","payload":"0","payloadType":"str","topic":"0","x":270,"y":380,"wires":[["df5f3151.22fd2","b7bb6b9e.2f0b88"]]},{"id":"4b0253e5.dafa8c","type":"function","z":"b517deb3.7318f","name":"get file","func":"var s = msg.payload.indexOf(\"Opening\");\nvar e = msg.payload.indexOf(\".mkv\");\nvar f =  msg.payload.substr(s + 9, e - s - 5);\nvar fa = flow.get(\"file_names\");\ns = fa.push(f);\ne = flow.get(\"total_files\");\nreturn {payload: f, count: s, max: e};","outputs":1,"noerr":0,"x":910,"y":480,"wires":[["875ce9b0.6fd128","dd165972.d87f08","852243f0.444a6"]]},{"id":"875ce9b0.6fd128","type":"ui_text","z":"b517deb3.7318f","group":"e996c6e3.977028","order":3,"width":0,"height":0,"name":"","label":"filename:","format":"{{msg.payload}}","layout":"row-spread","x":1060,"y":480,"wires":[]},{"id":"11fdbe17.12b8d2","type":"function","z":"b517deb3.7318f","name":"get frame","func":"var o = msg.payload.indexOf(\"fps=\") - 1;\no = msg.payload.substring(6, o).trim();\no = parseInt(o) % flow.get(\"total_frames\");\nvar f = Math.floor(1000 * o / flow.get(\"total_frames\"))/10;\nreturn {payload: f, frames: o};","outputs":1,"noerr":0,"x":920,"y":400,"wires":[["cf5df7e4.4599b8","a018d5f0.1b6e78"]]},{"id":"b7bb6b9e.2f0b88","type":"link out","z":"b517deb3.7318f","name":"outDVRStart","links":["de150998.30ca88"],"x":355,"y":420,"wires":[]},{"id":"de150998.30ca88","type":"link in","z":"b517deb3.7318f","name":"inDVRInfo","links":["b7bb6b9e.2f0b88","3320c833.2a9fc8"],"x":955,"y":360,"wires":[["51a2fa6.b4f2704","ac489b89.4c3eb8","875ce9b0.6fd128","cf5df7e4.4599b8","b2b9a424.bc9e58","a018d5f0.1b6e78"]]},{"id":"680778b9.11ce18","type":"ui_button","z":"b517deb3.7318f","name":"","group":"4fc6d69.aab3928","order":8,"width":0,"height":0,"passthru":true,"label":"Stop","color":"","bgcolor":"","icon":"","payload":"0","payloadType":"str","topic":"0","x":270,"y":460,"wires":[["b7bb6b9e.2f0b88","afb9a0cd.973b7"]]},{"id":"4762e56a.080c2c","type":"change","z":"b517deb3.7318f","name":"kill","rules":[{"t":"set","p":"kill","pt":"msg","to":"SIGTERM","tot":"str"}],"action":"","property":"","from":"","to":"","reg":false,"x":530,"y":460,"wires":[["eba846a3.c3ea48"]]},{"id":"cf5df7e4.4599b8","type":"ui_gauge","z":"b517deb3.7318f","name":"","group":"e996c6e3.977028","order":1,"width":0,"height":0,"gtype":"donut","title":"","label":"%","format":"{{value}}","min":0,"max":"100","colors":["#00b500","#e6e600","#ca3838"],"seg1":"","seg2":"","x":1050,"y":400,"wires":[]},{"id":"780c74d8.75425c","type":"alexa-local","z":"b517deb3.7318f","devicename":"Recording","inputtrigger":false,"x":110,"y":360,"wires":[["20d0dd6c.a1af82"]]},{"id":"20d0dd6c.a1af82","type":"switch","z":"b517deb3.7318f","name":"","property":"payload","propertyType":"msg","rules":[{"t":"cont","v":"on","vt":"str"},{"t":"cont","v":"off","vt":"str"}],"checkall":"true","repair":false,"outputs":2,"x":130,"y":420,"wires":[["8783cd49.ddabd"],["680778b9.11ce18"]]},{"id":"9025fca6.4e13b","type":"ui_numeric","z":"b517deb3.7318f","name":"","label":"length (min):","group":"4fc6d69.aab3928","order":1,"width":0,"height":0,"passthru":true,"topic":"","format":"{{value}}","min":"1","max":"60","step":"1","x":410,"y":100,"wires":[["e53d8381.b1469"]]},{"id":"e53d8381.b1469","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"fps\", 10)\nflow.set(\"total_frames\", msg.payload * 60 * flow.get(\"fps\"));\nreturn null;","outputs":1,"noerr":0,"x":550,"y":100,"wires":[[]]},{"id":"1a8c6398.4efc0c","type":"ui_numeric","z":"b517deb3.7318f","name":"files","label":"files to loop:","group":"4fc6d69.aab3928","order":2,"width":0,"height":0,"passthru":true,"topic":"","format":"{{value}}","min":0,"max":"100","step":"1","x":390,"y":140,"wires":[["8387ecf1.d9113"]]},{"id":"8387ecf1.d9113","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"total_files\", msg.payload);\nreturn null;","outputs":1,"noerr":0,"x":550,"y":140,"wires":[[]]},{"id":"852243f0.444a6","type":"ui_text","z":"b517deb3.7318f","group":"e996c6e3.977028","order":4,"width":0,"height":0,"name":"","label":"files:","format":"{{msg.count}}","layout":"row-spread","x":1330,"y":520,"wires":[]},{"id":"dd165972.d87f08","type":"switch","z":"b517deb3.7318f","name":"","property":"count","propertyType":"msg","rules":[{"t":"gt","v":"max","vt":"msg"}],"checkall":"true","repair":false,"outputs":1,"x":1190,"y":480,"wires":[["89a94db6.0da05"]]},{"id":"89a94db6.0da05","type":"function","z":"b517deb3.7318f","name":"shift file","func":"var fa = flow.get(\"file_names\");\nreturn {payload: fa.shift()};","outputs":1,"noerr":0,"x":1320,"y":480,"wires":[["852243f0.444a6","352016e4.42a08a"]]},{"id":"352016e4.42a08a","type":"fs-ops-delete","z":"b517deb3.7318f","name":"Delete","path":"path","pathType":"flow","filename":"payload","filenameType":"msg","x":1190,"y":420,"wires":[["bfb98e73.e2b1"]]},{"id":"f885ed2d.a5f8","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"path\", msg.payload)\nreturn null;","outputs":1,"noerr":0,"x":550,"y":60,"wires":[[]]},{"id":"6866964f.67a098","type":"ui_text_input","z":"b517deb3.7318f","name":"","label":"output path:","group":"4fc6d69.aab3928","order":5,"width":0,"height":0,"passthru":true,"mode":"text","delay":300,"topic":"","x":410,"y":60,"wires":[["f885ed2d.a5f8"]]},{"id":"f6943934.ab8d68","type":"inject","z":"b517deb3.7318f","name":"onStart","topic":"0","payload":"0","payloadType":"num","repeat":"","crontab":"","once":true,"onceDelay":"1","x":120,"y":60,"wires":[["3da6dc87.f960d4","5d1229f5.ceb358","55ac2769.019618","ae541916.0ca4a8","3c381355.f8a94c","379dd0.234db23","56550b8c.65bb04"]]},{"id":"3da6dc87.f960d4","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: \"/home/admin/local/cam/\"};","outputs":1,"noerr":0,"x":250,"y":60,"wires":[["6866964f.67a098"]]},{"id":"5d1229f5.ceb358","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: 10};","outputs":1,"noerr":0,"x":250,"y":100,"wires":[["9025fca6.4e13b"]]},{"id":"55ac2769.019618","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: 25};","outputs":1,"noerr":0,"x":250,"y":140,"wires":[["1a8c6398.4efc0c"]]},{"id":"b2b9a424.bc9e58","type":"ui_text","z":"b517deb3.7318f","group":"e996c6e3.977028","order":6,"width":0,"height":0,"name":"","label":"errors:","format":"{{msg.payload}}","layout":"row-spread","x":910,"y":520,"wires":[]},{"id":"a018d5f0.1b6e78","type":"ui_text","z":"b517deb3.7318f","group":"e996c6e3.977028","order":2,"width":0,"height":0,"name":"","label":"frames:","format":"{{msg.frames}}","layout":"row-spread","x":1060,"y":360,"wires":[]},{"id":"ae541916.0ca4a8","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: \"rtsp://192.168.101.120/unicast\"};","outputs":1,"noerr":0,"x":250,"y":180,"wires":[["af7275fd.f12b88"]]},{"id":"11dd233e.66998d","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"input\", msg.payload);\nreturn null;","outputs":1,"noerr":0,"x":550,"y":180,"wires":[[]]},{"id":"af7275fd.f12b88","type":"ui_text_input","z":"b517deb3.7318f","name":"","label":"input stream:","group":"4fc6d69.aab3928","order":4,"width":0,"height":0,"passthru":true,"mode":"text","delay":300,"topic":"","x":410,"y":180,"wires":[["11dd233e.66998d"]]},{"id":"3c381355.f8a94c","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: \"%04Y-%02m-%02d-%02H-%02M-CAM.mkv\"};","outputs":1,"noerr":0,"x":250,"y":220,"wires":[["9fa0d367.e67b"]]},{"id":"9bb4b2f4.53044","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"output\", msg.payload);\nflow.set(\"pid\", 0);\nreturn null;","outputs":1,"noerr":0,"x":550,"y":220,"wires":[[]]},{"id":"9fa0d367.e67b","type":"ui_text_input","z":"b517deb3.7318f","name":"","label":"output mask:","group":"4fc6d69.aab3928","order":6,"width":0,"height":0,"passthru":true,"mode":"text","delay":300,"topic":"","x":410,"y":220,"wires":[["9bb4b2f4.53044"]]},{"id":"94a5ee38.0d334","type":"fs-ops-dir","z":"b517deb3.7318f","name":"Dir","path":"path","pathType":"flow","filter":"*.mkv","filterType":"str","dir":"payload","dirType":"msg","x":530,"y":360,"wires":[["7b25e5.5b455a1c"]]},{"id":"7b25e5.5b455a1c","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"file_names\", msg.payload)\nreturn {count: msg.payload.length};","outputs":1,"noerr":0,"x":650,"y":360,"wires":[["4855e585.d6110c"]]},{"id":"3320c833.2a9fc8","type":"link out","z":"b517deb3.7318f","name":"outDVRStart","links":["5030be04.4d3d9","de150998.30ca88"],"x":355,"y":300,"wires":[]},{"id":"4855e585.d6110c","type":"link out","z":"b517deb3.7318f","name":"ourDir","links":["5030be04.4d3d9"],"x":735,"y":360,"wires":[]},{"id":"5030be04.4d3d9","type":"link in","z":"b517deb3.7318f","name":"inFile","links":["2c875a46.4a0ab6","4855e585.d6110c","3320c833.2a9fc8"],"x":1235,"y":520,"wires":[["852243f0.444a6"]]},{"id":"829a73f.026fa9","type":"switch","z":"b517deb3.7318f","name":"","property":"continue","propertyType":"flow","rules":[{"t":"true"}],"checkall":"true","repair":true,"outputs":1,"x":530,"y":400,"wires":[["90aa707f.bc633"]]},{"id":"379dd0.234db23","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload:0, frames: 0, count: 0};","outputs":1,"noerr":0,"x":250,"y":300,"wires":[["3320c833.2a9fc8"]]},{"id":"df5f3151.22fd2","type":"change","z":"b517deb3.7318f","name":"true","rules":[{"t":"set","p":"continue","pt":"flow","to":"true","tot":"bool"}],"action":"","property":"","from":"","to":"","reg":false,"x":390,"y":380,"wires":[["829a73f.026fa9","94a5ee38.0d334"]]},{"id":"afb9a0cd.973b7","type":"change","z":"b517deb3.7318f","name":"false","rules":[{"t":"set","p":"continue","pt":"flow","to":"false","tot":"bool"}],"action":"","property":"","from":"","to":"","reg":false,"x":390,"y":460,"wires":[["4762e56a.080c2c"]]},{"id":"56550b8c.65bb04","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: \"08:00:00\"};","outputs":1,"noerr":0,"x":250,"y":260,"wires":[["ab89297b.f0ac18"]]},{"id":"25028b2e.f9a1a4","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"total_time\", msg.payload);\nreturn null;","outputs":1,"noerr":0,"x":550,"y":260,"wires":[[]]},{"id":"ab89297b.f0ac18","type":"ui_text_input","z":"b517deb3.7318f","name":"restart","label":"restart every (HH:MM:SS):","group":"4fc6d69.aab3928","order":3,"width":0,"height":0,"passthru":true,"mode":"text","delay":300,"topic":"","x":390,"y":260,"wires":[["25028b2e.f9a1a4"]]},{"id":"bfb98e73.e2b1","type":"function","z":"b517deb3.7318f","name":"get count","func":"var fa = flow.get(\"file_names\");\nreturn {count: fa.length, max: flow.get(\"total_files\")};","outputs":1,"noerr":0,"x":1320,"y":420,"wires":[["dd165972.d87f08"]]},{"id":"e996c6e3.977028","type":"ui_group","z":"","name":"Progress","tab":"b735df7.b412f2","order":2,"disp":true,"width":"6","collapse":false},{"id":"4fc6d69.aab3928","type":"ui_group","z":"","name":"Recorder","tab":"b735df7.b412f2","order":1,"disp":true,"width":"6","collapse":false},{"id":"b735df7.b412f2","type":"ui_tab","z":"","name":"DVR","icon":"dashboard"}]```
