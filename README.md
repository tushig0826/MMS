<p align="center">
  <img src="https://github.com/tushig0826/MMS/blob/main/screenshots/BME.PNG" />
</p>

<p align="center">**Budapest University of Technology and Economics**</p>
<p align="center">Faculty of Electrical Engineering and Informatics</p>
<p align="center">Department of Telecommunications and Media Informatics</p>
<br/>
<br/>
<br/>
<p align="center">WebRTC Laboratory Report</p>
<br/>
<br/>
<p align="center">Student:  Bat-Erdene Tushig	    	Neptun#: QBI3JH	</p>
<br/>
<br/>
<p align="center">Field: 	Computer Engineering     Specialization: Infocommunication </p>
<p align="center">Github: https://github.com/tushig0826/MMS</p>
<br/>
<br/>
<p align="center">Date: 2023 October 20</p>
<br/>
<br/>
<br/>
<br/>
<p align="center">

  **Task 1.1**

The following code snippet demonstrates the fixed bugs in chat.js file:

callButton.onclick = call;
setBandwidthButton.onclick = setBandwidth;
pc.onicecandidate = onLocalICECandidateGenerated;
pc.oniceconnectionstatechange = onIceConnectionStateChange;

![figure1](https://github.com/tushig0826/MMS/blob/main/screenshots/figure_1.PNG)
<p align="center">Figure 1. Result after fixing the bug</p>

**Task 1.2:**
WebRTC connection it has to be defined:
var pc = new RTCPeerConnection();
To establish a connection, caller has to call which means that has to create an offer. In other words, when the user clicks on a call button, the call function will be called and it sets the calling variable to true. Then it creates an offer based on the pc.createOffer() function:
function call() {
  callButton.disabled = true;
  calling = true;

  pc.createOffer()
    .then(onCreateOfferSuccess)
    .catch(onError);
}
setBandwidth function will be called if the user clicks on a button and it will set up the parameters for making it the maximum bitrate of the video stream.
onSignallingMessage will work if a signaling message is received from a remote peer. If OnSignallingMessage is a messaging offer it has to set the description on the RTC session.
If ice_candidate triggered, new ice candidate peer will be received from the remote caller. Msg object also contains media description, stream identification and candidate attributes.
```ruby
function onSignallingMessage(msg) {
  switch(msg.type) {
    case 'offer':
      callButton.disabled = true;

      pc.setRemoteDescription(new RTCSessionDescription(JSON.parse(msg.data)))
        .then(onSetRemoteDescriptionSuccess)
        .catch(onError);
      break;

    case 'ice_candidate':
      var candidate = new RTCIceCandidate({
        sdpMLineIndex: msg.sdpMLineIndex,
        sdpMid: msg.sdpMid,
        candidate: msg.candidate});

      pc.addIceCandidate(candidate)
        .then(onAddIceCandidateSuccess)
        .catch(onError);
      break;
  }
}
```
setRemoteDescription() will set up the RTC session description.

gotLocalStream is called when local stream is obtained, and gotRemoteStream when remote stream is obtained.
```ruby
function gotLocalStream(stream) {
  localVideo.srcObject = stream;
  stream.getTracks().forEach(track => pc.addTrack(track, stream));
  callButton.disabled = false;
}

function gotRemoteStream(event) {
  remoteVideo.srcObject = event.streams[0];

  pc.getSenders().forEach(sender => {console.log(sender);})
  setBandwidthButton.disabled = false;
}
```
onSetLocalDescriptionSuccess doesn’t to anything but it is called when local session description has been set. onSetRemoteDescription behaves the same but in remote and it creates an answer to the offer that has been called by remote caller using pc.createAnswer().
```ruby
function onSetLocalDescriptionSuccess() {}

function onSetRemoteDescriptionSuccess() {
  if(!calling) {
    pc.createAnswer()
      .then(onCreateAnswerSuccess)
      .catch(onError);
  }
}
```
It is called when creating an offer is successful. Offer object sets local session description.
```ruby
function onCreateOfferSuccess(offer) {
  pc.setLocalDescription(offer)
    .then(onSetLocalDescriptionSuccess)
    .catch(onError);

  signalling.send({
    type: 'offer',
    data: JSON.stringify(offer)
  });
}
```
Answer is called when answer to the offer is created successfully. Answer object also sets local description and using signalling,send() to send object to the caller. 
```ruby
function onCreateAnswerSuccess(answer) {
  pc.setLocalDescription(answer)
    .then(onSetLocalDescriptionSuccess)
    .catch(onError);

  signalling.send({
    type: 'offer',
    data: JSON.stringify(answer)
  });
}
```
New ICE candidate is generated. It sends a candidate to a remote caller or peer.
```ruby
function onLocalICECandidateGenerated(event) {
  if (event.candidate) {
    console.log('ICE candidate generated: ', event.candidate);
    signalling.send({
      type: 'ice_candidate',
      sdpMLineIndex: event.candidate.sdpMLineIndex,
      sdpMid: event.candidate.sdpMid,
      candidate: event.candidate.candidate
    });
  }
}
```
It is triggered when a new candidate has been added.
```ruby
function onAddIceCandidateSuccess() {
  console.log('Ice candidate added successfully');
}
```
When there is a change in the current state of connection of the candidate, this function will be called.
```ruby
function onIceConnectionStateChange(event) {
  console.log(pc.iceConnectionState);
}
```

**Task 1.3:**

Session Description Protocol is for describing multimedia sessions such as session announcement, session invitation, and so on. 
![figure2](https://github.com/tushig0826/MMS/blob/main/screenshots/figure_2.PNG)
<p align="center">Figure 2: Overview of SDP with the minimal needed messages for a connection setup source: [ http://help.aarenet.com/wiki/Support_voip_protocol ] </p>
As you can see from the figure 2, the SDP answer/offer model is used by two devices or users to reach acknowledgement on the description of the session. 
The offering side indicates the desired session description in the offer with codec list and port.
The answering side replies to the offer with the desired session description from its own viewpoint. If the offering side receives the answer with expected codec and port or information then acknowledgement will be sent and connection will be established. 

Task 2.1:
```ruby
function onLocalICECandidateGenerated(event) {
  if (event.candidate) {
    console.log('ICE candidate generated: ', event.candidate);
    signalling.send({
      type: 'ice_candidate',
      sdpMLineIndex: event.candidate.sdpMLineIndex,
      sdpMid: event.candidate.sdpMid,
      candidate: event.candidate.candidate
    });
  }
}

function onAddIceCandidateSuccess() {
  console.log('Ice candidate added successfully');
}

function onIceConnectionStateChange(event)f {
  console.log(pc.iceConnectionState);
}
```
![figure3](https://github.com/tushig0826/MMS/blob/main/screenshots/figure_3.PNG)
<p align="center">Figure 3. Setted bandwidth 5000 </p>
![figure4](https://github.com/tushig0826/MMS/blob/main/screenshots/figure_4.PNG)
<p align="center"> Figure 4. Console output </p>
In the above figure, It demonstrates if ICE candidate generated (console.log('ICE candidate generated: ', event.candidate);) and adding ICE candidate was successful (console.log('Ice candidate added successfully');).




**Task 2.2:**
Both resolutions written below were supported.
```ruby
navigator.mediaDevices.getUserMedia({
  audio: false,
  video: {
    width: { min: 640 },
    height: { min: 480 }
  }
}).then(gotLocalStream)
  .catch(onError);

```
```ruby
navigator.mediaDevices.getUserMedia({
  audio: false,
  video: {
    width: { min: 800 },
    height: { min: 600 }
  }
}).then(gotLocalStream)
  .catch(onError);
```

![figure5](https://github.com/tushig0826/MMS/blob/main/screenshots/figure_5.PNG)
<p align="center"> Figure 5: Result </p>
We can also modify HTML files highlighted sections to change height and width:

```ruby
video {
      position: relative;
      left: 50%;
      transform: translateX(-50%);
      width: 400px;
      height: 400px; 
      }
```

















**Task 2.3:**
![figure6](https://github.com/tushig0826/MMS/blob/main/screenshots/figure_6.PNG)
<p align="center"> Figure 6. Webrtc-internals tools to check degradation </p>
Left side is our first caller where the bandwidth set to 50000 kb/s
(file:///C:/Users/tushig.baterdene/Downloads/WebRTC/MMS-main/chat.html [ rid: 106, lid: 4, pid: 14284 ])  and right side is second caller where the bandwidth set to 100000 kb/s(file:///C:/Users/tushig.baterdene/Downloads/WebRTC/MMS-main/chat.html [ rid: 103, lid: 5, pid: 17396 ])
In the following figure 7, the values of the callers where bandwidth has been set to 50000 kb/s is 12.089, and 100000 kb/s is 20.15 in quality limitation duration.

![figure7](https://github.com/tushig0826/MMS/blob/main/screenshots/figure_6.PNG)
<p align="center"> Figure 7. Quality Limitation duration with bandwidth</p>









**Task 2.4:**
![figure8](https://github.com/tushig0826/MMS/blob/main/screenshots/diagram_v2.png)
<p align="center"> Figure 8. Architecture diagram </p>
