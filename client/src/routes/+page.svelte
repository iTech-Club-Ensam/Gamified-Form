<script lang="ts">
	import {onMount} from 'svelte';
	import axios from 'axios';


	let formSwitch:boolean=false;
	let thanksSwitch:boolean=false;
	let joinedSwitch:boolean=false;
	let emailExists:boolean=false;

	const iconsMap = new Map();
	iconsMap.set("Water", "https://img.icons8.com/ios/50/000000/water-element.png");
	iconsMap.set("Fire", "https://img.icons8.com/ios/50/000000/fire-element.png");
	iconsMap.set("Earth", "https://img.icons8.com/ios/50/000000/earth-element.png");
	iconsMap.set("Air", "https://img.icons8.com/ios/50/000000/air-element.png");

	let fullName:string;
	let email:string;
	let filiere:string;	

	const teamArray = ["Water", "Fire", "Earth", "Air"];
	let team:string
	let theme:string="crimson";

	const getRandomTeam = () => {
		team = teamArray[Math.floor(Math.random() * teamArray.length)];
	};

	let joinPromise:Promise<void>
	
	function setTheme(t:string) {
		switch (t) {
			case "Water":
				theme = "wintry";
				break;
			case "Fire":
				theme = "crimson";
				break;
			case "Earth":
				theme = "sahara";
				break;
			case "Air":
				theme = "seafoam";
				break;
			default:
				theme = "wintry";
		}	
	}
	let seconds = 5;
	async function join() {
		if(!fullName || !email || !filiere) return
		if(filiere === "0") return alert("Please select your major");
		try {
			joinPromise = axios.post("https://api.itech-club.com/join", {fullName, email, filiere, team});
			const res = await joinPromise;
			if (res.status === 200) {
				thanksSwitch = true;
				localStorage.setItem("joined", "true");
				for (let i = 0; i < 5; i++) {
					await new Promise(r => setTimeout(r, 1000));
					seconds--;
					if (seconds === 0) {
						window.location.replace("https://chat.whatsapp.com/GVfvRuq9AIpJema4dMgPCT");
					}
				}
				//new Promise(r => setTimeout(r, 5000)).then(() => window.location.replace("https://chat.whatsapp.com/GVfvRuq9AIpJema4dMgPCT"));
			}
		} catch (error) {
			if (error.response.status === 402) {
				emailExists = true;
				localStorage.setItem("joined", "true");
			}
		}
		// const res = await axios.post("http://localhost:8000/join", {fullName, email, filiere, team});
		// if (res.status === 200) {
		// 	thanksSwitch = true;
		// 	localStorage.setItem("joined", "true");
		// 	new Promise(r => setTimeout(r, 6000)).then(() => window.open("https://chat.whatsapp.com/GVfvRuq9AIpJema4dMgPCT"));
		// }
	}

	let randomIcon:string = "https://img.icons8.com/ios/50/000000/launched-rocket.png"
	async function iconRandomizer() {
		for (let i = 0; i < 100; i++) {
			const thisTeam = teamArray[Math.floor(Math.random() * teamArray.length)];
			await new Promise(r => setTimeout(r, 65));
			team = thisTeam
			setTheme(thisTeam)
			randomIcon = iconsMap.get(thisTeam);
		}
		await new Promise(r => setTimeout(r, 600));
		randomIcon = ""	
	}

	async function setCookies() {
		document.cookie = "team=" + team + "; max-age=31536000;";
	}
	
	
	
	
	onMount(async () => {
		if (document.cookie.includes("team")) {
			localStorage.getItem("joined")=="true" ? joinedSwitch = true : joinedSwitch = false;
			team = document.cookie.split("team=")[1].split(";")[0];
			randomIcon=""
			setTheme(team)
		} else {
			await iconRandomizer()
			await setCookies()
		}
	})

</script>

<style>
	.teamDistributer {
		align-items: center;
		justify-content: center;
		display: flex;
		flex-direction: column;
		height: 100vh;
	}
	.teamDistributer h1 {
		font-size: 2rem;
		margin-bottom: 2rem;
		text-align: center;
		line-height: normal;
	}
	.teamDistributer button {
		font-size: 1rem;
	}
	.teamDistributer form {
		display: flex;
		flex-direction: column;
		gap: 1rem;
	}
	.teamDistributer .teamText {
		flex-direction: row;
	}
	.formlogo {
		width: 7rem;
		height: auto;
		margin-bottom: 2rem;
		/* position: absolute;
		width: 6rem;
		margin-bottom: 2rem;
		bottom: 0; */
	}
	.loading {
		display: flex;
		flex-direction: row;
		gap: 1rem;
		justify-content: center;
		align-items: center;
		box-shadow: 0 0 0.5rem 0.25rem rgba(0, 0, 0, 0.1);
	}
	.emailexists {
		color: #D4163C;
		text-align: center;
	}
	.pforsuccess {
		margin-left: 2rem;
		margin-right: 2rem;
	}
	.socialIcons {
		display: flex;
		flex-direction: row;
		gap: 2rem;
		justify-content: center;
		align-items: center;
	}
	.btn-icon {
		background-color: #fff;
		border: 1px solid #000;
		border-radius: 50%;
		padding: 0.5rem;
		margin-top: 2rem;
	}
</style>

<body data-theme={theme}>
<div class="teamDistributer">
	{#if randomIcon != "" && randomIcon != undefined}
			
			<div class="rotating icons">
				<img src={randomIcon} alt="{randomIcon} icon" />
			</div>
	{:else}
		<img class="formlogo" src="logo.png" alt="iTech logo">
		<h1 class="teamText">You are team {@html team?team:'<div class="placeholder animate-pulse flex-0" />'}</h1>
		{#if thanksSwitch || emailExists || joinedSwitch}
			{#if emailExists}
				<p class="emailexists">Email already exists</p>
			{:else}
				<h2>Thank you for joining!</h2>
				{#if !joinedSwitch}
					<p class="pforsuccess" style="text-align: center;">An email was sent to your inbox. Please check your spam folder if you don't see it.</p>
					<br>
					<p class="pforsuccess" style="text-align: center;">You will be redirected to our whatsapp channel in {seconds} seconds.</p>
				{:else}
					<p class="pforsuccess" style="text-align: center;">You are already a member of our community.</p>
				{/if}
			{/if}
		{:else}
			{#await joinPromise}
			<section class="card w-50 animate-pulse">
				<div class="loading p-3 ">
					<div class="placeholder-circle w-4 h-4" />
					<div class="placeholder-circle w-4 h-4" />
					<div class="placeholder-circle w-4 h-4" />
				</div>
			</section>
			{:then response}
			{#if formSwitch == false}
				{#if emailExists}
					<p class="emailexists">Email already exists</p>
				{:else}
					<button  type="button" class="btn variant-filled-primary" on:click={()=>{formSwitch=true}}>Join Now</button>
				{/if}
			{:else}
				<form on:submit|preventDefault={join}>
					<input class="input" type="text" required bind:value={fullName} placeholder="Your name" >
					<input class="input" type="email" required bind:value={email} placeholder="your@email.com">
					
					<select class="select" required bind:value={filiere}>
						<option value="0" disabled selected>Select your Major</option>
						<option value="1">Master IT</option>
						<option value="2">Master IDMS</option>
						<option value="9">Master BD</option>
						<option value="3">Génie IAGI</option>
						<option value="4">Génie MI</option>
						<option value="6">Génie MSEI</option>
						<option value="7">Genie Industriel</option>
						<option value="10">Génie EM</option>
						<option value="8">Cycle préparatoire</option>
					</select>
						

					<button type="submit" class="btn variant-filled-primary">Join Now</button>
				</form>
			{/if}
				
			{/await}
		{/if}
		<!-- <h3 class="h1" style="margin-bottom:2rem;">The event is over!</h3>
		<p class="pforsuccess" style="text-align: center;">We were happy to see you. And we will surely do it again!!!</p>
		<br>
		<p class="pforsuccess" style="text-align: center;">We welcome you this Friday the 8th and throught the whole following week, from 12h00 to 13h00, in our stand if you want to join our club</p>
		<br>
		<p class="pforsuccess" style="text-align: center;">For more information, Feel free to join the open community of itech Club via these Social links</p>
		<div class="socialIcons">
			<button type="button" class="btn-icon variant-filled "><a href="https://www.instagram.com/itech__club/"><img src="https://img.icons8.com/ios/50/000000/instagram-new.png" alt="instagram" /></a></button>
			<button type="button" class="btn-icon variant-filled"><a href="https://chat.whatsapp.com/GVfvRuq9AIpJema4dMgPCT"><img src="https://img.icons8.com/ios/50/000000/whatsapp.png" alt="whatsapp" /></a></button>
		</div> -->
	{/if}
	
</div>
</body>