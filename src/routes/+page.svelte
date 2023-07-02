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
			'output.webm'
		);
		const data = ffmpeg.FS('readFile', 'output.webm');
		const video = document.getElementById('player');
		video.src = URL.createObjectURL(new Blob([data.buffer], { type: 'video/webm' }));
	};
</script>

<video id="player" controls />
<input type="file" id="uploader" />
