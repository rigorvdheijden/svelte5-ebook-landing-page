<script>
	import { loadStripe } from '@stripe/stripe-js';
	import { PUBLIC_STRIPE_KEY } from '$env/static/public';
	import { goto } from '$app/navigation';

	let { children, ...props } = $props();

	async function onclick() {
		try {
			const stripe = await loadStripe(PUBLIC_STRIPE_KEY);

			// STEP 1: Issue a POST request to the back-end server
			const response = await fetch('api/checkout', {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json'
				}
			});

			// STEP 4: Wait until back-end returns the session ID
			const { sessionId } = await response.json();

			// STEP 5: Redirect to the stripe checkout page using sesssion ID
			await stripe.redirectToCheckout({ sessionId });
		} catch (err) {
			goto('/checkout/failure');
		}
	}
</script>

<button {...props} {onclick}>{@render children()}</button>

<style>
	button {
		background-color: black;
		color: white;
		padding: 20px 20px;
		font-weight: normal;
		font-size: 22px;
		text-transform: uppercase;
		transition: all 0.3s;
		border: solid 1px white;
	}

	button:hover {
		background-color: white;
		color: black;
	}
</style>
