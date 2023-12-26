<script lang="ts">
	import { writable } from 'svelte/store';
	import { browser } from '$app/environment';

	const userId = writable(browser && localStorage.getItem('userId'));

	console.log({ $userId });



	let srcToChange = 'https://firebasestorage.googleapis.com/v0/b/aiavatars-d061c.appspot.com/o/diosd.mp4?alt=media&token=4e1c5579-f49c-4f30-b14d-8f303ba92031'


	let showButton = true;
	let statusText = 'Started'
	userId.subscribe((value) => {
		browser && localStorage.setItem('userId', value), console.log('UserId changed:', value);
	});
	function downloadFile() {
		// Replace this with the URL or content of the file you want to download
		const fileUrl = srcToChange;

		// Create a link element
		const link = document.createElement('a');
		link.href = fileUrl;

		// Specify the filename for the download
		link.download = 'GreetGenius_video';

		// Append the link to the document
		document.body.appendChild(link);

		// Trigger a click on the link to start the download
		link.click();

		// Remove the link from the document
		document.body.removeChild(link);
	}

	async function copyToClipboard() {
		try {
			await navigator.clipboard.writeText(srcToChange);
			//  copyButtonText = 'Copied!';
			
		} catch (err) {
			console.error('Unable to copy to clipboard', err);
			//  copyButtonText = 'Copy failed';
		}
	}
	let responseData;
  let audioUrl;
  let textToConvert = 'Merry Christmas, and good luck scientist!';
  async function fetchData() {
  try {
	showButton = false;
    const apiKey = '2108305a1f7bcbc64bfd98e9f05832a7';
    const voiceId = 'yGGC0mRhco1oH1j28hh6';

    const apiUrl = `https://api.elevenlabs.io/v1/text-to-speech/${voiceId}`;
	statusText = 'Generating voice'
    const apiRequestOptions = {
      method: 'POST',
      headers: {
        accept: 'audio/mpeg',
        'content-type': 'application/json',
        'xi-api-key': apiKey,
      },
      body: JSON.stringify({
        text: textToConvert,
      }),
      responseType: 'arraybuffer',
    };

    const response = await fetch(apiUrl, apiRequestOptions);
    const audioData = await response.arrayBuffer();

    // Convert the audio Blob to Data URI
    const audioBlob = new Blob([audioData], { type: 'audio/mpeg' });
    const reader = new FileReader();
    reader.readAsDataURL(audioBlob);
    reader.onloadend = () => {
      const audioDataUri = reader.result;
	  statusText = 'Generating face'
      // Send the audioDataUri to the generateImage API
      fetch('https://replicate-ganr.onrender.com/generateImage', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          audio: audioDataUri,
          face: 'https://ucarecdn.com/675bead2-fd07-4987-81b7-4d25822913d1/Untitleddesign134.png',
          // Add other parameters as needed
        }),
      })
        .then(generateImageResponse => generateImageResponse.json())
        .then(generateImageResult => {
			statusText = 'Loading video'
          console.log('generateImage API response:', generateImageResult['result']);

		  srcToChange = generateImageResult['result'];

// Get the video element
const videoElement = document.getElementById('myVideo');

videoElement.src = generateImageResult['result'];
// Wait for metadata to load, then play the video
videoElement.addEventListener('loadedmetadata', () => {
  videoElement.play();
});
showButton = true
        })
        .catch(error => {
          alert('Error fetching data:', error.message);
        });
    };
  } catch (error) {
    alert('Error fetching data:', error.message);
  }
}


</script>


<div class="grid grid-cols-1 md:grid-cols-2 p-8">
	<!-- ... -->

	<!--
  Heads up! 👋

  Plugins:
    - @tailwindcss/forms
-->

	<div
		class="mx-auto max-w-screen-xl md:w-2/3 w-full mb-6 bg-white rounded-md px-4 py-16 sm:px-6 lg:px-8"
	>
		<div class="mx-auto max-w-lg text-center">
			<h1 class="text-2xl font-bold mb-3 sm:text-3xl">Get started today!</h1>
			<div role="alert" class="rounded border-s-4 border-indigo-700 bg-indigo-50 p-4">
				<strong class="block font-medium text-indigo-700"> Please be mindful! </strong>

				<p class="mt-2 text-sm text-indigo-500">
					This service is free to use, we do not take responsibility for what you generate and the
					purpose for it is not to spread fake information.
				</p>
			</div>
		</div>

		<form action="" class="mx-auto mb-0 mt-8 max-w-md space-y-4">
			<div>
				<label for="OrderNotes" class="block text-sm font-medium text-gray-700">
					What do you want the character to say?
				</label>

				<textarea
					id="OrderNotes"
					class="mt-2 w-full md:min-h-[250px] rounded-lg border-gray-200 align-top shadow-sm sm:text-sm"
					rows="4"
					placeholder="Enter any additional order notes..."
				    bind:value={textToConvert}
					></textarea>

					{#if showButton}
					<button  on:click={fetchData} id="myButton" class="w-full mt-5 inline-block rounded-lg bg-[#121114] px-5 py-3 text-sm font-medium text-white">
					  Generate
					</button>
				  {/if}
				  
				  {#if !showButton}
					<div class="flex items-center justify-between">
					  <p class="text-sm text-gray-500">Status</p>
						<p class='text-sm text-green-500'>{statusText}</p>

					</div>
				  {/if}	
		</div>

			<div class="flex items-center justify-between">
				<p class="text-sm text-gray-500">
					Put your PR or internal comms on autopilot <br />
				</p>

				<button
					type="submit"
					class="inline-block rounded-lg bg-[#121114] px-5 py-3 text-sm font-medium text-white"
				>
					Sign up
				</button>
			</div>
		</form>
	</div>

	<div class="flex justify-center">
		<div class="rounded-lg shadow-lg max-w-sm">
			<a href="#!">
				<!-- svelte-ignore a11y-media-has-caption -->
				<video width="320" height="240" id="myVideo" controls class="w-full rounded-t-lg">
					<source
					    src={srcToChange}
						type="video/mp4"
					/>
					<source src="movie.ogg" type="video/ogg" />
					Your browser does not support the video tag.
				</video>
			</a>
			<div class="text-center mt-3">
				<span class="inline-flex overflow-hidden rounded-md border bg-white shadow-sm">
					<button
						on:click={() => downloadFile()}
						class=" inline-block border-e p-3 text-gray-700 hover:bg-gray-50 focus:relative"
						title="Edit Product"
					>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="icon icon-tabler icon-tabler-download"
							width="24"
							height="24"
							viewBox="0 0 24 24"
							stroke-width="2"
							stroke="currentColor"
							fill="none"
							stroke-linecap="round"
							stroke-linejoin="round"
							><path stroke="none" d="M0 0h24v24H0z" fill="none" /><path
								d="M4 17v2a2 2 0 0 0 2 2h12a2 2 0 0 0 2 -2v-2"
							/><path d="M7 11l5 5l5 -5" /><path d="M12 4l0 12" /></svg
						>
					</button>

					<button
						on:click={() => copyToClipboard()}
						class="inline-block p-3 text-gray-700 hover:bg-gray-50 focus:relative"
						title="Delete Product"
					>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="icon icon-tabler icon-tabler-share-2"
							width="24"
							height="24"
							viewBox="0 0 24 24"
							stroke-width="2"
							stroke="currentColor"
							fill="none"
							stroke-linecap="round"
							stroke-linejoin="round"
							><path stroke="none" d="M0 0h24v24H0z" fill="none" /><path
								d="M8 9h-1a2 2 0 0 0 -2 2v8a2 2 0 0 0 2 2h10a2 2 0 0 0 2 -2v-8a2 2 0 0 0 -2 -2h-1"
							/><path d="M12 14v-11" /><path d="M9 6l3 -3l3 3" /></svg
						>
					</button>
				</span>
			</div>
		</div>
	</div>
</div>
