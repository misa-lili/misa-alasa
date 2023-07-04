<script lang="ts">
	import { createFFmpeg, fetchFile, type FFmpeg } from '@ffmpeg/ffmpeg';
	import '98.css';
	import Modal from '$lib/components/Modal.svelte';
	import emitter from '$lib/emitter';
	import { onMount } from 'svelte';
	import JSZip from 'jszip';

	interface EncodedFile {
		id: string;
		filename: string;
		length: number;
		pts: number;
		bytes: number;
		ext: string;
		status: 'SUCCESS' | 'ERROR';
		blob: Blob;
		bitrate: number;
	}

	let encodedFiles: EncodedFile[] = [];
	$: encodedFiles;

	let bitrate = 256;
	let ffmpeg: FFmpeg | null = null;

	onMount(async () => {
		ffmpeg = createFFmpeg();
		ffmpeg.setLogger(({ type, message }) => {
			const textarea = window.document.getElementById('text19')! as HTMLTextAreaElement;
			textarea.value += `${type}: ${message}\n`;
			textarea.scrollTop = textarea.scrollHeight;
		});
		await ffmpeg.load();
	});

	const submit = async () => {
		const files = (document.querySelector('input#uploader') as HTMLInputElement).files!;

		if (files.length === 0) {
			return emitter.emit('openModal', { type: 'error', message: '파일을 선택해주세요.' });
		}

		for await (const file of files) {
			const time = 2.98;
			const duration = (await checkDuration(file)) || 0;
			const pts = duration > time ? duration / time : 1;

			ffmpeg!.FS('writeFile', file.name, await fetchFile(file));
			await ffmpeg!.run(
				'-i',
				file.name,
				'-row-mt',
				'1',
				'-filter:v',
				`setpts=PTS/${pts}`,
				'-r',
				'30',
				'-t',
				`${time}`,
				'-an',
				'-c:v',
				'libvpx-vp9',
				'-pix_fmt',
				'yuva420p',
				'-s',
				'512x512',
				'-b:v',
				`${bitrate}`,
				'output.webm'
			);
			const data = ffmpeg!.FS('readFile', 'output.webm');
			const blob = new Blob([data.buffer], { type: 'video/webm' });
			const encodedFile: EncodedFile = {
				id: crypto.randomUUID(),
				filename: file.name,
				length: duration < time ? duration : time,
				pts,
				bytes: blob.size,
				ext: 'webm',
				status: 'SUCCESS',
				blob,
				bitrate
			};
			if (blob.size > 255000) {
				return emitter.emit('openModal', { type: 'error', message: '256kb가 넘습니다.' });
			}
			encodedFiles = [...encodedFiles, encodedFile];
		}
	};

	const checkDuration = (file: Blob): Promise<number> => {
		return new Promise((resolve) => {
			if (file.type.includes('image')) {
				resolve(0);
			}
			var video = document.createElement('video');
			video.preload = 'metadata';

			video.onloadedmetadata = () => {
				window.URL.revokeObjectURL(video.src);
				var duration = video.duration;
				video.remove();
				resolve(duration);
			};

			video.src = URL.createObjectURL(file);
		});
	};

	const reset = () => {
		const uploader: HTMLInputElement | null = window.document.querySelector('input#uploader');
		uploader!.value = '';

		const textarea: HTMLTextAreaElement | null = window.document.querySelector('textarea#text19');
		textarea!.value = '';
	};

	const downloadAll = async () => {
		if (encodedFiles.length === 0) {
			return emitter.emit('openModal', { type: 'error', message: '파일을 선택해주세요.' });
		}

		if (encodedFiles.length === 1) {
			return download(encodedFiles[0].blob);
		}

		let zip = new JSZip();

		const blobs = encodedFiles.map((f) => f.blob);
		const filenames = encodedFiles.map((f) => f.filename);

		for (let i = 0; i < blobs.length; i++) {
			let blobData = await blobs[i].arrayBuffer();
			zip.file(filenames[i] + '.webm', blobData);
		}

		let zipBlob = await zip.generateAsync({ type: 'blob' });

		let url = window.URL.createObjectURL(zipBlob);
		let link = document.createElement('a');
		link.href = url;
		link.download = 'files.zip';
		link.click();
	};

	const deleteAll = () => {
		encodedFiles = [];
	};

	const deleteFile = (id: string) => {
		encodedFiles = encodedFiles.filter((file) => file.id !== id);
	};

	const download = (blob: Blob) => {
		const url = window.URL.createObjectURL(blob);
		const a = document.createElement('a');
		a.href = url;
		a.download = crypto.randomUUID() + '.webm';
		document.body.appendChild(a);
		a.click();
		document.body.removeChild(a);
	};
</script>

<Modal />

<div class="window container" style="width:280px; margin: 0 auto;">
	<div class="title-bar">
		<div class="title-bar-text">텔레그램 스티커 생성기</div>
		<div class="title-bar-controls">
			<button aria-label="Minimize" />
			<button aria-label="Maximize" />
			<button aria-label="Close" />
		</div>
	</div>
	<div class="window-body" style="overflow:hidden;">
		<menu role="tablist">
			<li role="tab" aria-selected="true"><a href="/manual">수동</a></li>
			<li role="tab" aria-selected="false"><a href="/arca">아카콘</a></li>
			<!-- <li role="tab" aria-selected="false"><a href="#tabs">디시콘</a></li>
			<li role="tab" aria-selected="false"><a href="#tabs">노벨콘</a></li>
			<li role="tab" aria-selected="false"><a href="#tabs">라인콘</a></li> -->
		</menu>
		<div class="window" role="tabpanel">
			<div class="window-body">
				<slot />
			</div>
		</div>

		<br />

		<br />

		<div class="field-row" style="width: 100%">
			<label for="range26">품질</label>
			<label for="range26">64k</label>
			<input id="range26" type="range" min="64" max="512" bind:value={bitrate} />
			<label for="range26">512k</label>
		</div>

		<br />

		<div class="field-row">
			<input id="radio5" type="radio" name="first-example" checked />
			<label for="radio5">로그 채널에 미공개</label>
		</div>
		<div class="field-row">
			<input id="radio6" type="radio" name="first-example" disabled />
			<label for="radio6">로그 채널에 공개</label>
		</div>

		<br />

		<div style="display: flex; width: 100%;">
			<input style="flex:1;" type="submit" on:click={submit} />
			<input style="flex:1;" type="reset" on:click={reset} />
		</div>

		<br />

		<div class="field-row-stacked" style="width: 100%">
			<label for="text19">로그</label>
			<textarea id="text19" rows="8" value="" readonly />
		</div>

		<br />

		<label for="status">상태</label>
		<div class="sunken-panel" id="status" style="height: 120px; width: 100%;">
			<table class="interactive">
				<thead>
					<tr>
						<th>파일이름</th>
						<th>길이</th>
						<th>배속</th>
						<th>용량</th>
						<th>비트레이트</th>
						<th>상태</th>
					</tr>
				</thead>
				<tbody>
					{#each encodedFiles as file (file.id)}
						<tr
							on:click={() => {
								// TODO: 모달로 변경
								if (confirm('삭제하시겠습니까?')) {
									deleteFile(file.id);
								}
							}}
						>
							<td>{file.filename}</td>
							<td>{file.length}</td>
							<td>{file.pts}</td>
							<td>{file.bytes}</td>
							<td>{file.bitrate}</td>
							<td>{file.status}</td>
						</tr>
					{/each}
				</tbody>
			</table>
		</div>
		<div style="display: flex;">
			<button style="flex: 1 1 0;" on:click={downloadAll}>모두 다운로드</button>
			<button style="flex: 1 1 0;" on:click={deleteAll}>모두 삭제</button>
		</div>
	</div>
</div>

<style>
	:global(body) {
		background: #dfdfdf;
	}
</style>
