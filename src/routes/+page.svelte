<script lang="ts">
	import { createFFmpeg, fetchFile } from '@ffmpeg/ffmpeg';
	import '98.css';
	import Modal from '$lib/components/Modal.svelte';
	import emitter from '$lib/emitter';
	import { onMount } from 'svelte';

	let encodedFiles: {
		id: string;
		filename: string;
		length: number;
		pts: number;
		bytes: number;
		ext: string;
		status: 'SUCCESS' | 'ERROR';
		blob: Blob;
		bitrate: number;
	} = [];
	$: encodedFiles;

	let bitrate = 256;
	let ffmpeg = null;

	onMount(async () => {
		ffmpeg = createFFmpeg();
		ffmpeg.setLogger(({ type, message }) => {
			const textarea = window.document.getElementById('text19');
			textarea.value += `${type}: ${message}\n`;
			textarea.scrollTop = textarea.scrollHeight;
		});
		await ffmpeg.load();
	});

	const submit = async () => {
		const files = document.getElementById('uploader').files;

		if (files.length === 0) {
			return emitter.emit('openModal', { type: 'error', message: '파일을 선택해주세요.' });
		}

		for await (const file of files) {
			const time = 2.98;
			const duration = (await checkDuration(file)) || 0;
			const pts = duration > time ? duration / time : 1;

			ffmpeg.FS('writeFile', file.name, await fetchFile(file));
			await ffmpeg.run(
				'-i',
				file.name,
				'-row-mt',
				'1',
				'-filter:v',
				`setpts=PTS/${pts}`,
				'-r',
				'30',
				// '-pattern_type',
				// 'glob',
				'-t',
				`${time}`,
				'-an',
				'-c:v',
				'libvpx-vp9',
				'-pix_fmt',
				'yuva420p',
				// '-vf',
				// 'scale=512:512',
				'-s',
				'512x512',
				'-b:v',
				`${bitrate}`,
				'output.webm'
			);
			const data = ffmpeg.FS('readFile', 'output.webm');
			const blob = new Blob([data.buffer], { type: 'video/webm' });
			const encodedFile = {
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
			if (bytes > 255000) {
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
		const uploader = window.document.getElementById('uploader');
		uploader.value = '';

		const textarea = window.document.getElementById('text19');
		textarea.value = '';
	};

	const downloadAll = () => {
		for (const file of encodedFiles) {
			download(file.blob);
		}
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
			<li role="tab" aria-selected="true"><a href="#tabs">수동</a></li>
			<!-- <li role="tab" aria-selected="false"><a href="#tabs">디시콘</a></li>
			<li role="tab" aria-selected="false"><a href="#tabs">노벨콘</a></li>
			<li role="tab" aria-selected="false"><a href="#tabs">라인콘</a></li> -->
		</menu>
		<div class="window" role="tabpanel">
			<div class="window-body">
				<p>어디 한번 잘 해보세요!</p>
				<ul>
					<li>스티커 셋의 최대 크기는 50개 입니다.</li>
					<li>품질을 조절해서 스티커의 용량을 조절해주세요.</li>
					<li>스티커의 용량은 256kb가 최대입니다.</li>
					<li>변환 후 다운로드를 해주세요.</li>
					<li>텔레그램에서 @Stickers 봇과 대화를 시작하세요.</li>
					<li>
						봇에게 /newvideo 명령어를 실행한 후, 변환하고 다운받은 .webm 파일을 파일 전송하세요.
					</li>
					<li>파일을 전송 한 후 적절한 이모티콘을 입력하세요.</li>
					<li>원하시는 만큼 반복하세요. 50개까지 가능합니다.</li>
					<li>파일의 비율은 1:1 이 아닐경우 찌그러질 수 있습니다.</li>
					<li>
						동영상일 경우 텔레그램은 3초가 최대이기 때문에 3초가 넘는 영상은 속도가 빨라지므로 미리
						잘라내야합니다.
					</li>
				</ul>

				<!-- <div class="field-row">
					<label for="text17">아카콘 번호</label>
					<input id="text17" type="text" />
				</div> -->
				<input type="file" id="uploader" accept="image/*,video/*" multiple />
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
