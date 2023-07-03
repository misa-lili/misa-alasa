<script lang="ts">
	import emitter from '$lib/emitter';
	emitter.on('openModal', (event) => open(event));
	emitter.on('closeModal', () => close());

	let type = '';
	let message = '';

	const open = (event) => {
		type = event.type;
		message = event.message;

		const modal = window.document.getElementById('modal');
		modal.style.display = 'block';
	};

	const close = () => {
		const modal = window.document.getElementById('modal');
		modal.style.display = 'none';
	};
</script>

<div
	id="modal"
	style="position:fixed; 
		left:0; 
		top:0; 
		display:none; 
		z-index: 99;
		width:100vw;
		height:100vh;
		background:rgba(0,0,0,0.3);
	"
>
	<div
		class="window"
		style="
		width: 250px; 
		position: fixed; 
		left: 50%; 
		top: 50%; 
		transform:translate(-50%, -50%); 
		z-index: 100;
	"
	>
		<div class="title-bar">
			<div class="title-bar-text">{type}</div>
			<div class="title-bar-controls">
				<!-- <button aria-label="Minimize" />
			<button aria-label="Maximize" /> -->
				<button aria-label="Close" on:click={close} />
			</div>
		</div>
		<div class="window-body">
			{message}
			<slot />
		</div>
	</div>
</div>
