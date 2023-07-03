<script>
	import { createFFmpeg, fetchFile } from '@ffmpeg/ffmpeg';
	import { onMount } from 'svelte';
	import '98.css';

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

<div class="window container" style="width:280px;">
	<div class="title-bar">
		<div class="title-bar-text">A Window With Stuff In It</div>
		<div class="title-bar-controls">
			<button aria-label="Minimize" />
			<button aria-label="Maximize" />
			<button aria-label="Close" />
		</div>
	</div>
	<div class="window-body" style="overflow:hidden;">
		<menu role="tablist">
			<li role="tab" aria-selected="false"><a href="#tabs">직접업로드</a></li>
			<li role="tab" aria-selected="true"><a href="#tabs">아카콘</a></li>
			<li role="tab" aria-selected="false"><a href="#tabs">디시콘</a></li>
			<li role="tab" aria-selected="false"><a href="#tabs">노벨콘</a></li>
			<li role="tab" aria-selected="false"><a href="#tabs">라인콘</a></li>
		</menu>
		<div class="window" role="tabpanel">
			<div class="window-body">
				<p>어디 한번 잘 해보세요!</p>
				<ul>
					<li>움직이는 스티커가 한 장이라도 포함된 경우 스티커 셋의 최대 크기는 50개 입니다.</li>
					<li>품질을 조절해서 스티커의 용량을 조절해주세요.</li>
					<li>움직이는 스티커의 경우 256kb가 최대입니다.</li>
				</ul>
			</div>
		</div>

		<br />
		<div class="field-row">
			<label for="text17">Occupation</label>
			<input id="text17" type="text" />
		</div>

		<div class="field-row" style="width: 100%">
			<label for="range25">품질</label>
			<label for="range26">64k</label>
			<input id="range26" type="range" min="1" max="11" value="5" />
			<label for="range27">512k</label>
		</div>

		<div class="field-row">
			<input id="radio5" type="radio" name="first-example" checked />
			<label for="radio5">로그 채널에 미공개</label>
		</div>
		<div class="field-row">
			<input id="radio6" type="radio" name="first-example" disabled />
			<label for="radio6">로그 채널에 공개</label>
		</div>

		<video id="player" controls width="100%" />

		<input type="file" id="uploader" />
		<div style="display: flex; width: 100%;">
			<input style="flex:1;" type="submit" />
			<input style="flex:1;" type="reset" />
		</div>

		<div class="sunken-panel" style="height: 120px; width: 100%;">
			<table class="interactive">
				<thead>
					<tr>
						<th>Name</th>
						<th>Version</th>
						<th>Company</th>
					</tr>
				</thead>
				<tbody>
					<tr>
						<td>MySQL ODBC 3.51 Driver</td>
						<td>3.51.11.00</td>
						<td>MySQL AB</td>
					</tr>
					<tr>
						<td>SQL Server</td>
						<td>3.70.06.23</td>
						<td>Microsoft Corporation</td>
					</tr>
					<tr>
						<td>SQL Server</td>
						<td>3.70.06.23</td>
						<td>Microsoft Corporation</td>
					</tr>
					<tr>
						<td>SQL Server</td>
						<td>3.70.06.23</td>
						<td>Microsoft Corporation</td>
					</tr>
					<tr>
						<td>SQL Server</td>
						<td>3.70.06.23</td>
						<td>Microsoft Corporation</td>
					</tr>
					<tr>
						<td>SQL Server</td>
						<td>3.70.06.23</td>
						<td>Microsoft Corporation</td>
					</tr>
					<tr>
						<td>SQL Server</td>
						<td>3.70.06.23</td>
						<td>Microsoft Corporation</td>
					</tr>
					<tr>
						<td>SQL Server</td>
						<td>3.70.06.23</td>
						<td>Microsoft Corporation</td>
					</tr>
					<tr>
						<td>SQL Server</td>
						<td>3.70.06.23</td>
						<td>Microsoft Corporation</td>
					</tr>
					<tr>
						<td>SQL Server</td>
						<td>3.70.06.23</td>
						<td>Microsoft Corporation</td>
					</tr>
				</tbody>
			</table>
		</div>
		<div class="field-row-stacked" style="width: 100%">
			<label for="text20">Additional notes</label>
			<textarea id="text20" rows="8" />
		</div>
	</div>
</div>

<style>
	:global(body) {
		background: #dfdfdf;
	}

	.container {
		width: 32rem;
	}
</style>
