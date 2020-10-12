<script>
	import {onMount} from "svelte";

	export let name;

	if ('serviceWorker' in navigator) {
		window.addEventListener('load', ()=> {
			navigator.serviceWorker.register('./sw.js').then(registration=> {
				console.log('ServiceWorker registration successful with scope: ', registration.scope);
			}, err => {
				console.log('ServiceWorker registration failed : ', err);
			})
		})
	}

	let deferredPrompt;
	onMount(()=>{

		window.addEventListener('beforeinstallprompt', function (e){
			console.log('beforeinstallprompt Event fired');
			e.preventDefault();

			deferredPrompt = e;

			return false;
		})
	})

	let btnEx;
	$: if (btnEx) {
		btnEx.addEventListener('click', function (){
			console.log('click!');
			console.log(deferredPrompt);
			if (deferredPrompt !== undefined) {
				deferredPrompt.prompt();

				deferredPrompt.userChoice.then(function (choiceResult) {
					console.log(choiceResult.outcome);

					if (choiceResult.outcome == 'dismissed') {
						console.log('User cancelled home screen install');
					} else {
						console.log('User added to home screen');
					}

					deferredPrompt = null;
				})
			}
		})
	}

</script>

<main>
	<h1>Hello {name}!</h1>
	<p>Visit the <a href="https://svelte.dev/tutorial">Svelte tutorial</a> to learn how to build Svelte apps.</p>
	<div bind:this={btnEx} style="width: 100px; height: 100px; border: 1px solid gray;">버튼</div>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>