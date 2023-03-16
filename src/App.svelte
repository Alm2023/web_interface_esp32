<script>
	import StatusLed from "./components/StatusLed.svelte";

	import { onMount, onDestroy } from "svelte";

	let status = undefined;
	let status2 = undefined;
	

	onMount(async () => {
		let lednum = 1;
		const res = await fetch(myurl + `/status/` + lednum);
		let myst = await res.json();
		status = myst.result > 0 ? true : false;

		lednum = 2;
		const res1 = await fetch(myurl + `/status/` + lednum);
		let myst1 = await res1.json();
		status2 = myst1.result > 0 ? true : false;
	});

	export let name;

	//let myurl = "http://192.168.1.96:5000";
	let myurl=""

	let promise; // = getRandomNumber(1,0);
	let promise2; //=getRandomNumber(2,0);
	async function getRandomNumber(lednum, action) {
		if (action == 0) {
			const res = await fetch(myurl + `/off/` + lednum);
			//const json = await res.text();
			const json = await res.json();
			if (res.ok) {
				//console.log(json)
				return json;
			} else {
				console.log(json);
				throw new Error(json);
			}
		} else {
			const res = await fetch(myurl + `/on/` + lednum);
			const json = await res.json();
			if (res.ok) {
				//console.log(json)
				return json;
			} else {
				console.log(json);
				throw new Error(json);
			}
		}
	}
	


	$: 	if (status) {
		
		
		
		promise = getRandomNumber(1, 1);
		
	} else if (!status && status != undefined) {
		
		
		promise = getRandomNumber(1, 0);
	}
	
	$: if (status2) {
		//console.log('TRUE');
		promise2 = getRandomNumber(2, 1);
	} else if (!status2 && status2 != undefined) {
		//console.log('FALSE')
		promise2 = getRandomNumber(2, 0);
	}

	let message1 = undefined;
	let message2 = undefined;

	function handlemessage(event) {
		if (event.detail.led == "1") {
			message1 = event.detail.statusresult>0?true:false;
		}
		if (event.detail.led == "2") {
			message2 = event.detail.statusresult>0?true:false;
		}
	}
</script>

<main>
	<h1>LED TOOL</h1>

	<!-- Rounded switch -->

	<div class="grid-container">
		<div class="header">status</div>
		<div class="header">Action</div>
		<div class="header">Action status</div>
		
		<div><StatusLed lednum="1" {myurl} on:message={handlemessage} /></div>

		<div>
			<label class="switch">
				<input type="checkbox" bind:checked={status} />
				<span class="slider round" />
			</label>
		</div>
		<div>
			{#if status != undefined}
				{#await promise}
					<div>...waiting</div>
				{:then myres}
					<div>Status: {myres.result}</div>
				{:catch error}
					<div style="color: red">{error.message}</div>
				{/await}
			{/if}
		</div>
		<div><StatusLed lednum="2" {myurl} on:message={handlemessage} /></div>
		<div>
			<label class="switch">
				<input type="checkbox" bind:checked={status2} />
				<span class="slider round" />
			</label>
		</div>
		<div>
			{#if status != undefined}
				{#await promise2}
					<div>...waiting</div>
				{:then myres}
					<div>Status: {myres.result}</div>
				{:catch error}
					<div style="color: red">{error.message}</div>
				{/await}
			{/if}
		</div>
		
	</div>
</main>

<style>
	.header {
		background-color: #abccae;
	}

	.grid-container {
		border-radius: 25px;
		display: grid;
		grid-template-columns: auto auto auto;
		gap: 8px;
		background-color: #eceedf;
		padding: 5x;
	}

	.flex-container {
		border-radius: 25px;
		display: flex;
		justify-content: left;
		align-items: center;
		align-content: left;
		background-color: #f1f1f1;
	}

	.flex-container > div {
		border-radius: 25px;
		background-color: #f0e9e9;
		margin: 3x;
		padding: 2px;
	}

	button {
		border-radius: 12px;
		font: larger;
		padding: 0.15rem 0.5rem;
		background-color: #7c74ee;
		border: none;
		outline: none;
		cursor: pointer;
		color: rgb(3, 3, 3);
	}
	button:hover {
		background-color: #928beb;
	}

	button:active {
		background-color: #4033f0;
		/*box-shadow: 0 5px #666;
  transform: translateY(4px);*/
	}
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

	.switch {
		position: relative;
		display: inline-block;
		width: 60px;
		height: 34px;
	}

	.switch input {
		opacity: 0;
		width: 0;
		height: 0;
	}

	.slider {
		position: absolute;
		cursor: pointer;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-color: #ccc;
		-webkit-transition: 0.4s;
		transition: 0.4s;
	}

	.slider:before {
		position: absolute;
		content: "";
		height: 26px;
		width: 26px;
		left: 4px;
		bottom: 4px;
		background-color: white;
		-webkit-transition: 0.4s;
		transition: 0.4s;
	}

	input:checked + .slider {
		background-color: #2196F3;
	}

	input:focus + .slider {
		box-shadow: 0 0 1px #2196F3;
	}

	input:checked + .slider:before {
		-webkit-transform: translateX(26px);
		-ms-transform: translateX(26px);
		transform: translateX(26px);
	}

	/* Rounded sliders */
	.slider.round {
		border-radius: 34px;
	}

	.slider.round:before {
		border-radius: 50%;
	}
</style>
