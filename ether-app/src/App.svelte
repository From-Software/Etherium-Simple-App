<script>
	import Greeter from './artifacts/contracts/Greeter.sol/Greeter.json';
	import { ethers } from 'ethers';
  const address = "0x5FbDB2315678afecb367f032d93F642f64180aa3";
  let greeting = '';
	let actual;

	async function requestAccount() {
    await window.ethereum.request({ method: 'eth_requestAccounts' });
  }

  async function fetchGreeting() {
    if (typeof window.ethereum !== 'undefined') {
      const provider = new ethers.providers.Web3Provider(window.ethereum)
      console.log({ provider })
      const contract = new ethers.Contract(address, Greeter.abi, provider)
      try {
        const data = await contract.greet()
        console.log('data: ', data)
				actual = data;
      } catch (err) {
        console.log("Error: ", err)
      }
    }
  }

	async function setGreeting() {
    if (!greeting) return
    if (typeof window.ethereum !== 'undefined') {
      await requestAccount()
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      console.log({ provider })
      const signer = provider.getSigner()
      const contract = new ethers.Contract(address, Greeter.abi, signer)
      const transaction = await contract.setGreeting(greeting)
      await transaction.wait()
      fetchGreeting()
    }
  }


</script>

<main>

	<div>
		<header>
			<button on:click={fetchGreeting}>Fetch Greeting</button>
			<button on:click={setGreeting}>Set Greeting</button>
			<input bind:value={greeting} placeholder="Set greeting">
			<h1>{greeting}</h1>
			<h1>The current phrase on the Blockchain is: "{actual}"</h1>
		</header>
	</div>
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