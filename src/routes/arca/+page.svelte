<script lang="ts">
	import { onMount } from 'svelte';

	onMount(() => {
		const menu = document.querySelector('menu') as HTMLMenuElement;
		for (let i = 0; i < menu.children.length; i++) {
			const item = menu.children.item(i);
			if (item.firstChild?.textContent === '아카콘') {
				item?.setAttribute('aria-selected', 'true');
			} else {
				item?.setAttribute('aria-selected', 'false');
			}
		}
	});
</script>

<p>돚거!</p>
<ul>
	<li>잠깐만요 아직 개발중입니다.</li>
</ul>

<div class="field-row">
	<label for="text17">아카콘 번호</label>
	<input id="text17" type="text" readonly value="01242551" />
</div>
