<script>
	import { onMount } from "svelte";
	import * as faceapi from "face-api.js";
  
	let video;
	let mood = "Unknown";
  
	async function loadModels() {
	  await faceapi.nets.tinyFaceDetector.loadFromUri("/models");
	  await faceapi.nets.faceExpressionNet.loadFromUri("/models");
	}
  
	async function detectMood() {
	  const detections = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions()).withFaceExpressions();
	  if (detections.length > 0) {
		mood = detections[0].expressions.asSortedArray()[0].expression;
	  }
	}
  
	onMount(async () => {
	  await loadModels();
	  navigator.mediaDevices
		.getUserMedia({ video: true })
		.then((stream) => {
		  video.srcObject = stream;
		  video.play();
		})
		.catch((err) => {
		  console.error("Error accessing webcam:", err);
		});
  
	  setInterval(detectMood, 1000);
	});
  </script>
  
  <main>
	<h1>Mood Decoder</h1>
	<video bind:this={video} width="640" height="480" autoplay></video>
	<p>Mood: {mood}</p>
  </main>
  
  <style>
	main {
	  display: flex;
	  flex-direction: column;
	  align-items: center;
	}
	video {
	  margin-top: 20px;
	}
	p {
	  margin-top: 20px;
	  font-size: 1.5em;
	}
  </style>