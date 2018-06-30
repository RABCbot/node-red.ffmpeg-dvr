# Node-RED-Ffmpeg-DVR
Live stream capture using FFMpeg segments in a ring buffer

```
[{"id":"5510c7a4.3778e8","type":"exec","z":"b517deb3.7318f","command":"ffmpeg","addpay":true,"append":"","useSpawn":"true","timer":"","oldrc":false,"name":"","x":650,"y":540,"wires":[[],["4128fb36.b1d9b4"],["798223c.f3d27dc","4128fb36.b1d9b4","df06bd32.8211a"]]},{"id":"4128fb36.b1d9b4","type":"switch","z":"b517deb3.7318f","name":"switch","property":"payload","propertyType":"msg","rules":[{"t":"cont","v":"frame=","vt":"str"},{"t":"cont","v":"Error","vt":"str"},{"t":"cont","v":"Invalid","vt":"str"},{"t":"cont","v":"extension","vt":"flow"}],"checkall":"true","repair":false,"outputs":4,"x":770,"y":520,"wires":[["a4870196.996b","b12012fd.088e1"],["f2dda3c7.32dbd"],["f2dda3c7.32dbd"],["ee1ac7a8.0bf228"]]},{"id":"a4870196.996b","type":"function","z":"b517deb3.7318f","name":"get time","func":"var i = msg.payload.indexOf(\"time=\");\nvar o =  msg.payload.substr(i + 5, 11);\nreturn {payload: o};","outputs":1,"noerr":0,"x":920,"y":520,"wires":[["b3776d97.7aae7"]]},{"id":"798223c.f3d27dc","type":"json","z":"b517deb3.7318f","name":"","property":"payload","action":"obj","pretty":false,"x":810,"y":620,"wires":[["e4087f17.51c74"]]},{"id":"b3776d97.7aae7","type":"ui_text","z":"b517deb3.7318f","group":"e996c6e3.977028","order":5,"width":0,"height":0,"name":"","label":"up time:","format":"{{msg.payload}}","layout":"row-spread","x":1060,"y":520,"wires":[]},{"id":"f88c889e.8c75f8","type":"function","z":"b517deb3.7318f","name":"cmd","func":"var s = \"-i \" + flow.get(\"input\");\ns += \" -vcodec copy -acodec copy -map 0\";\ns += \" -t \" + flow.get(\"total_time\");\ns += \" -f segment -strftime 1 -reset_timestamps 1 -segment_time \";\ns += Math.trunc(flow.get(\"total_frames\") / flow.get(\"fps\"));\ns += \" \" + flow.get(\"path\");\ns += flow.get(\"output\");\ns += flow.get(\"extension\");\nreturn {payload: s};","outputs":1,"noerr":0,"x":650,"y":480,"wires":[["5510c7a4.3778e8","dd67821a.4c1"]]},{"id":"294c6bba.c0ca64","type":"ui_text","z":"b517deb3.7318f","group":"e996c6e3.977028","order":7,"width":0,"height":0,"name":"rc","label":"return code:","format":"{{msg.payload}}","layout":"row-spread","x":1050,"y":620,"wires":[]},{"id":"e4087f17.51c74","type":"change","z":"b517deb3.7318f","name":"code","rules":[{"t":"set","p":"payload","pt":"msg","to":"payload.code","tot":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":930,"y":620,"wires":[["294c6bba.c0ca64"]]},{"id":"f014f4f.8b1ea08","type":"ui_button","z":"b517deb3.7318f","name":"","group":"4fc6d69.aab3928","order":8,"width":0,"height":0,"passthru":true,"label":"Start","color":"","bgcolor":"","icon":"","payload":"0","payloadType":"str","topic":"0","x":270,"y":460,"wires":[["80075d1b.2ff69","6f9714a7.63e0fc"]]},{"id":"b12012fd.088e1","type":"function","z":"b517deb3.7318f","name":"get frame","func":"var o = msg.payload.indexOf(\"fps=\") - 1;\no = msg.payload.substring(6, o).trim();\no = parseInt(o) % flow.get(\"total_frames\");\nvar f = Math.floor(1000 * o / flow.get(\"total_frames\"))/10;\nreturn {payload: f, frames: o};","outputs":1,"noerr":0,"x":920,"y":480,"wires":[["a0555cb6.00f23","f07dfe9d.e96f9"]]},{"id":"6f9714a7.63e0fc","type":"link out","z":"b517deb3.7318f","name":"outDVRStart","links":["c3efb811.447cf8"],"x":355,"y":500,"wires":[]},{"id":"c3efb811.447cf8","type":"link in","z":"b517deb3.7318f","name":"inDVRInfo","links":["6f9714a7.63e0fc","373c1741.c5a328"],"x":955,"y":440,"wires":[["b3776d97.7aae7","294c6bba.c0ca64","a0555cb6.00f23","f2dda3c7.32dbd","f07dfe9d.e96f9"]]},{"id":"b6efc042.0b886","type":"ui_button","z":"b517deb3.7318f","name":"","group":"4fc6d69.aab3928","order":9,"width":0,"height":0,"passthru":true,"label":"Stop","color":"","bgcolor":"","icon":"","payload":"0","payloadType":"str","topic":"0","x":270,"y":540,"wires":[["6f9714a7.63e0fc","68ad680d.77fcb8"]]},{"id":"30569c6b.0971e4","type":"change","z":"b517deb3.7318f","name":"kill","rules":[{"t":"set","p":"kill","pt":"msg","to":"SIGTERM","tot":"str"}],"action":"","property":"","from":"","to":"","reg":false,"x":530,"y":540,"wires":[["5510c7a4.3778e8"]]},{"id":"a0555cb6.00f23","type":"ui_gauge","z":"b517deb3.7318f","name":"","group":"e996c6e3.977028","order":1,"width":0,"height":0,"gtype":"donut","title":"","label":"%","format":"{{value}}","min":0,"max":"100","colors":["#00b500","#e6e600","#ca3838"],"seg1":"","seg2":"","x":1050,"y":480,"wires":[]},{"id":"c5fc2ed8.96418","type":"alexa-local","z":"b517deb3.7318f","devicename":"Recording","inputtrigger":false,"x":110,"y":440,"wires":[["7a519562.ffc43c"]]},{"id":"7a519562.ffc43c","type":"switch","z":"b517deb3.7318f","name":"","property":"payload","propertyType":"msg","rules":[{"t":"cont","v":"on","vt":"str"},{"t":"cont","v":"off","vt":"str"}],"checkall":"true","repair":false,"outputs":2,"x":130,"y":500,"wires":[["f014f4f.8b1ea08"],["b6efc042.0b886"]]},{"id":"9d0e8e50.b7935","type":"ui_numeric","z":"b517deb3.7318f","name":"","label":"length (min):","group":"4fc6d69.aab3928","order":1,"width":0,"height":0,"passthru":true,"topic":"","format":"{{value}}","min":"1","max":"60","step":"1","x":410,"y":80,"wires":[["7f56c8ba.2e39f8"]]},{"id":"7f56c8ba.2e39f8","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"fps\", 10)\nflow.set(\"total_frames\", msg.payload * 60 * flow.get(\"fps\"));\nreturn null;","outputs":1,"noerr":0,"x":550,"y":80,"wires":[[]]},{"id":"18b7b783.c0e318","type":"ui_numeric","z":"b517deb3.7318f","name":"files","label":"files to loop:","group":"4fc6d69.aab3928","order":2,"width":0,"height":0,"passthru":true,"topic":"","format":"{{value}}","min":0,"max":"100","step":"1","x":390,"y":120,"wires":[["e76bb637.54bbe8"]]},{"id":"e76bb637.54bbe8","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"total_files\", msg.payload);\nreturn null;","outputs":1,"noerr":0,"x":550,"y":120,"wires":[[]]},{"id":"15765287.f278ad","type":"ui_text","z":"b517deb3.7318f","group":"e996c6e3.977028","order":4,"width":0,"height":0,"name":"","label":"files:","format":"{{msg.count}}","layout":"row-spread","x":590,"y":700,"wires":[]},{"id":"aa98b3ec.3c4ac","type":"switch","z":"b517deb3.7318f","name":"","property":"count","propertyType":"msg","rules":[{"t":"gt","v":"max","vt":"msg"},{"t":"else"}],"checkall":"true","repair":true,"outputs":2,"x":430,"y":700,"wires":[["15765287.f278ad","cca46a82.8f1e08"],["15765287.f278ad"]]},{"id":"8135f31.e75971","type":"function","z":"b517deb3.7318f","name":"shift","func":"fa = flow.get(\"file_names\")\nreturn {payload: fa.shift()};","outputs":1,"noerr":0,"x":510,"y":640,"wires":[["12315c99.45bc43"]]},{"id":"12315c99.45bc43","type":"fs-ops-delete","z":"b517deb3.7318f","name":"Delete","path":"path","pathType":"flow","filename":"payload","filenameType":"msg","x":630,"y":640,"wires":[["7458cd8d.7c98c4"]]},{"id":"b7981f2e.0feff","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"path\", msg.payload)\nreturn null;","outputs":1,"noerr":0,"x":550,"y":40,"wires":[[]]},{"id":"3be38990.eb90c6","type":"ui_text_input","z":"b517deb3.7318f","name":"","label":"output path:","group":"4fc6d69.aab3928","order":5,"width":0,"height":0,"passthru":true,"mode":"text","delay":300,"topic":"","x":410,"y":40,"wires":[["b7981f2e.0feff"]]},{"id":"1e152fb0.bb422","type":"inject","z":"b517deb3.7318f","name":"onStart","topic":"0","payload":"0","payloadType":"num","repeat":"","crontab":"","once":true,"onceDelay":"1","x":120,"y":40,"wires":[["58bf711f.ac5e","7cbd3b77.1f5b74","d56ae498.edb898","737928ea.474db8","30ef2405.0db26c","10a688c2.263f87","78daeadc.d27b24","57a2c609.a9a508","55374b62.ccab54"]]},{"id":"58bf711f.ac5e","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: \"/home/admin/local/cam/\"};","outputs":1,"noerr":0,"x":250,"y":40,"wires":[["3be38990.eb90c6"]]},{"id":"7cbd3b77.1f5b74","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: 20};","outputs":1,"noerr":0,"x":250,"y":80,"wires":[["9d0e8e50.b7935"]]},{"id":"d56ae498.edb898","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: 70};","outputs":1,"noerr":0,"x":250,"y":120,"wires":[["18b7b783.c0e318"]]},{"id":"f2dda3c7.32dbd","type":"ui_text","z":"b517deb3.7318f","group":"d4221c66.4afed","order":6,"width":0,"height":0,"name":"","label":"Message:","format":"{{msg.payload}}","layout":"row-spread","x":1060,"y":560,"wires":[]},{"id":"f07dfe9d.e96f9","type":"ui_text","z":"b517deb3.7318f","group":"e996c6e3.977028","order":2,"width":0,"height":0,"name":"","label":"frames:","format":"{{msg.frames}}","layout":"row-spread","x":1060,"y":440,"wires":[]},{"id":"737928ea.474db8","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: \"rtsp://192.168.101.120/unicast\"};","outputs":1,"noerr":0,"x":250,"y":160,"wires":[["6c84bfc8.11d43"]]},{"id":"419ffe87.e65f7","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"input\", msg.payload);\nreturn null;","outputs":1,"noerr":0,"x":550,"y":160,"wires":[[]]},{"id":"6c84bfc8.11d43","type":"ui_text_input","z":"b517deb3.7318f","name":"","label":"input stream:","group":"4fc6d69.aab3928","order":4,"width":0,"height":0,"passthru":true,"mode":"text","delay":300,"topic":"","x":410,"y":160,"wires":[["419ffe87.e65f7"]]},{"id":"30ef2405.0db26c","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: \"%04Y-%02m-%02d-%02H-%02M-CAM\"};","outputs":1,"noerr":0,"x":250,"y":200,"wires":[["9c5f0521.49ad08"]]},{"id":"6240bebc.70b7a","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"output\", msg.payload);\nflow.set(\"pid\", 0);\nreturn null;","outputs":1,"noerr":0,"x":550,"y":200,"wires":[[]]},{"id":"9c5f0521.49ad08","type":"ui_text_input","z":"b517deb3.7318f","name":"","label":"output mask:","group":"4fc6d69.aab3928","order":6,"width":0,"height":0,"passthru":true,"mode":"text","delay":300,"topic":"","x":410,"y":200,"wires":[["6240bebc.70b7a"]]},{"id":"7458cd8d.7c98c4","type":"fs-ops-dir","z":"b517deb3.7318f","name":"Dir","path":"path","pathType":"flow","filter":"extension","filterType":"flow","dir":"payload","dirType":"msg","x":190,"y":700,"wires":[["ebd76ee8.3c788","5f4fde36.11301"]]},{"id":"373c1741.c5a328","type":"link out","z":"b517deb3.7318f","name":"outDVRStart","links":["b0737fe3.23a85","c3efb811.447cf8"],"x":355,"y":360,"wires":[]},{"id":"b0737fe3.23a85","type":"link in","z":"b517deb3.7318f","name":"inFiles","links":["373c1741.c5a328"],"x":515,"y":740,"wires":[["15765287.f278ad"]]},{"id":"df06bd32.8211a","type":"switch","z":"b517deb3.7318f","name":"","property":"continue","propertyType":"flow","rules":[{"t":"true"}],"checkall":"true","repair":true,"outputs":1,"x":530,"y":480,"wires":[["f88c889e.8c75f8"]]},{"id":"10a688c2.263f87","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload:0, frames: 0, count: 0};","outputs":1,"noerr":0,"x":250,"y":360,"wires":[["373c1741.c5a328"]]},{"id":"80075d1b.2ff69","type":"change","z":"b517deb3.7318f","name":"true","rules":[{"t":"set","p":"continue","pt":"flow","to":"true","tot":"bool"}],"action":"","property":"","from":"","to":"","reg":false,"x":390,"y":460,"wires":[["df06bd32.8211a"]]},{"id":"68ad680d.77fcb8","type":"change","z":"b517deb3.7318f","name":"false","rules":[{"t":"set","p":"continue","pt":"flow","to":"false","tot":"bool"}],"action":"","property":"","from":"","to":"","reg":false,"x":390,"y":540,"wires":[["30569c6b.0971e4"]]},{"id":"78daeadc.d27b24","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: \"03:00:00\"};","outputs":1,"noerr":0,"x":250,"y":240,"wires":[["2e71b63e.90f86a"]]},{"id":"c5d56271.b1a3d","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"total_time\", msg.payload);\nreturn null;","outputs":1,"noerr":0,"x":550,"y":240,"wires":[[]]},{"id":"2e71b63e.90f86a","type":"ui_text_input","z":"b517deb3.7318f","name":"restart","label":"restart every (HH:MM:SS):","group":"4fc6d69.aab3928","order":3,"width":0,"height":0,"passthru":true,"mode":"text","delay":300,"topic":"","x":390,"y":240,"wires":[["c5d56271.b1a3d"]]},{"id":"ebd76ee8.3c788","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"file_names\", msg.payload)\nreturn {\n    payload: msg.payload, \n    count: msg.payload.length, \n    max: flow.get(\"total_files\")\n};","outputs":1,"noerr":0,"x":310,"y":700,"wires":[["aa98b3ec.3c4ac"]]},{"id":"647592ad.adf6bc","type":"catch","z":"b517deb3.7318f","name":"","scope":["bc7a22d7.a632a","e738911f.290b2","f88c889e.8c75f8","e4087f17.51c74","12315c99.45bc43","7458cd8d.7c98c4","a0555cb6.00f23","68ad680d.77fcb8","5510c7a4.3778e8","e92fc2cf.f5283","5f4fde36.11301","18b7b783.c0e318","15765287.f278ad","f07dfe9d.e96f9","b12012fd.088e1","a4870196.996b","dd67821a.4c1","c3efb811.447cf8","46e33e8f.1e97e","b0737fe3.23a85","10a688c2.263f87","57a2c609.a9a508","30ef2405.0db26c","737928ea.474db8","78daeadc.d27b24","55374b62.ccab54","d56ae498.edb898","7cbd3b77.1f5b74","58bf711f.ac5e","6c84bfc8.11d43","798223c.f3d27dc","30569c6b.0971e4","9d0e8e50.b7935","f2dda3c7.32dbd","1e152fb0.bb422","6f9714a7.63e0fc","373c1741.c5a328","ee1ac7a8.0bf228","9c5f0521.49ad08","3be38990.eb90c6","294c6bba.c0ca64","c5fc2ed8.96418","2e71b63e.90f86a","e76bb637.54bbe8","419ffe87.e65f7","6240bebc.70b7a","7f56c8ba.2e39f8","c5d56271.b1a3d","48c1ec44.c4b234","ebd76ee8.3c788","b7981f2e.0feff","8135f31.e75971","f014f4f.8b1ea08","b6efc042.0b886","7a519562.ffc43c","cca46a82.8f1e08","df06bd32.8211a","4128fb36.b1d9b4","aa98b3ec.3c4ac","bc464922.e713d8","80075d1b.2ff69","b3776d97.7aae7"],"x":920,"y":560,"wires":[["f2dda3c7.32dbd"]]},{"id":"e738911f.290b2","type":"function","z":"b517deb3.7318f","name":"cmd","func":"var s = \"-ss 10 -i \" + flow.get(\"path\") + msg.payload[0];\ns += \" -an -vf select=\\'eq(pict_type\\\\,I)\\' -vframes 1 \";\ns += flow.get(\"path\") + msg.payload[0].replace(flow.get(\"extension\"), \".jpg\");\nreturn {payload: s};","outputs":1,"noerr":0,"x":270,"y":600,"wires":[["e92fc2cf.f5283"]]},{"id":"e92fc2cf.f5283","type":"exec","z":"b517deb3.7318f","command":"ffmpeg","addpay":true,"append":"","useSpawn":"true","timer":"","oldrc":false,"name":"","x":390,"y":600,"wires":[[],[],["8135f31.e75971"]]},{"id":"ee1ac7a8.0bf228","type":"link out","z":"b517deb3.7318f","name":"outFile","links":["46e33e8f.1e97e"],"x":835,"y":580,"wires":[]},{"id":"46e33e8f.1e97e","type":"link in","z":"b517deb3.7318f","name":"inFile","links":["ee1ac7a8.0bf228"],"x":95,"y":700,"wires":[["7458cd8d.7c98c4"]]},{"id":"57a2c609.a9a508","type":"function","z":"b517deb3.7318f","name":"init","func":"return {payload: false};","outputs":1,"noerr":0,"x":250,"y":280,"wires":[["bc464922.e713d8"]]},{"id":"bc464922.e713d8","type":"ui_switch","z":"b517deb3.7318f","name":"","label":"thumbnail","group":"4fc6d69.aab3928","order":7,"width":0,"height":0,"passthru":true,"decouple":"false","topic":"","style":"","onvalue":"true","onvalueType":"bool","onicon":"","oncolor":"","offvalue":"false","offvalueType":"bool","officon":"","offcolor":"","x":400,"y":280,"wires":[["48c1ec44.c4b234"]]},{"id":"48c1ec44.c4b234","type":"function","z":"b517deb3.7318f","name":"set","func":"flow.set(\"thumbnail\", msg.payload);\nreturn null;","outputs":1,"noerr":0,"x":550,"y":280,"wires":[[]]},{"id":"cca46a82.8f1e08","type":"switch","z":"b517deb3.7318f","name":"","property":"thumbnail","propertyType":"flow","rules":[{"t":"true"},{"t":"else"}],"checkall":"true","repair":false,"outputs":2,"x":150,"y":620,"wires":[["e738911f.290b2"],["8135f31.e75971"]]},{"id":"55374b62.ccab54","type":"function","z":"b517deb3.7318f","name":"init","func":"flow.set(\"extension\", \".mkv\");\nreturn {payload: null};","outputs":1,"noerr":0,"x":250,"y":320,"wires":[[]]},{"id":"dd67821a.4c1","type":"http request","z":"b517deb3.7318f","name":"","method":"POST","ret":"txt","url":"http://192.168.101.120/cgi-bin/action?cmd=reboot","tls":"","x":670,"y":420,"wires":[[]]},{"id":"bc7a22d7.a632a","type":"box out","z":"b517deb3.7318f","box":"","filename":"","localFilename":"","name":"","x":430,"y":740,"wires":[]},{"id":"5f4fde36.11301","type":"function","z":"b517deb3.7318f","name":"file","func":"var n = msg.payload.length - 2;\nreturn {\n    localFilename: flow.get(\"path\") + msg.payload[n], \n    filename: \"Camera/\" + msg.payload[n]\n};","outputs":1,"noerr":0,"x":310,"y":740,"wires":[["bc7a22d7.a632a"]]},{"id":"e996c6e3.977028","type":"ui_group","z":"","name":"Progress","tab":"e4500ce.1d07ef","order":2,"disp":true,"width":"6","collapse":false},{"id":"4fc6d69.aab3928","type":"ui_group","z":"","name":"Recorder","tab":"e4500ce.1d07ef","order":1,"disp":true,"width":"6","collapse":false},{"id":"d4221c66.4afed","type":"ui_group","z":"","name":"Errors","tab":"e4500ce.1d07ef","disp":true,"width":"6","collapse":false},{"id":"e4500ce.1d07ef","type":"ui_tab","z":"","name":"DVR","icon":"dashboard","order":2}]
```
