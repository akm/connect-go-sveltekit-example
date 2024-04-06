<script lang="ts">
	import { createPromiseClient } from '@connectrpc/connect';
	import { createConnectTransport } from '@connectrpc/connect-web';

	// Import service definition that you want to connect to.
	import { GreetService } from '../gen/greet/v1/greet_connect';

	// The transport defines what type of endpoint we're hitting.
	// In our example we'll be communicating with a Connect endpoint.
	// If your endpoint only supports gRPC-web, make sure to use
	// `createGrpcWebTransport` instead.
	const transport = createConnectTransport({
		baseUrl: 'http://localhost:8080'
	});

	// Here we make the client itself, combining the service
	// definition with the transport.
	const client = createPromiseClient(GreetService, transport);

	let inputValue = '';

	type Message = {
		fromMe: boolean;
		message: string;
	};
	let messages: Message[] = [];
</script>

{#each messages as message}
	<div>{message.fromMe ? 'Name: ' : 'Greeting: '}{message.message}</div>
{/each}

<form
	on:submit={async (e) => {
		const response = await client.greet({ name: inputValue });
		messages = [
			...messages,
			{ fromMe: true, message: inputValue },
			{ fromMe: false, message: response.greeting }
		];
		inputValue = '';
	}}
>
	<input bind:value={inputValue} />
	<button type="submit">Send</button>
</form>
