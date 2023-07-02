<script>
	import { createFFmpeg, fetchFile } from '@ffmpeg/ffmpeg';
	import { onMount } from 'svelte';

	onMount(() => {
		document.getElementById('uploader').addEventListener('change', transcode);
	});

	const transcode = async ({ target: { files } }) => {
		const ffmpeg = createFFmpeg({ log: true });
		const { name } = files[0];
		await ffmpeg.load();
		ffmpeg.FS('writeFile', name, await fetchFile(files[0]));
		await ffmpeg.run(
			'-i',
			name,
			'-r',
			'30',
			'-t',
			'2.99',
			'-an',
			'-c:v',
			'libvpx-vp9',
			'-pix_fmt',
			'yuva420p',
			'-s',
			'512x512',
			'-row-mt',
			'1',
			'output.webm'
		);
		const data = ffmpeg.FS('readFile', 'output.webm');
		const video = document.getElementById('player');
		const source = document.createElement('source');
		const url = URL.createObjectURL(new Blob([data.buffer]));
		source.setAttribute('src', url);
		source.setAttribute('type', 'video/webm');

		video.appendChild(source);
		video.load();
		video.play();
		clickFunction(url);
	};

	function clickFunction(url) {
		const a = document.createElement('a');
		a.href = url;
		a.download = crypto.randomUUID() + '.webm';
		document.body.appendChild(a);
		a.click();
		document.body.removeChild(a);
	}
</script>

<video id="player" controls />
<input type="file" id="uploader" />
