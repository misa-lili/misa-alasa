<script>
	import { createFFmpeg, fetchFile } from '@ffmpeg/ffmpeg';
	import { onMount } from 'svelte';
	import '98.css';

	onMount(() => {
		document.getElementById('uploader').addEventListener('change', transcode);
	});

	const transcode = async ({ target: { files } }) => {
		const ffmpeg = createFFmpeg();
		ffmpeg.setLogger(({ type, message }) => {
			console.log({ type, message });
			const textarea = window.document.getElementById('text19');
			textarea.value += `${type}: ${message}\n`;
			textarea.scrollTop = textarea.scrollHeight;
		});
		const { name } = files[0];
		await ffmpeg.load();
		ffmpeg.FS('writeFile', name, await fetchFile(files[0]));
		await ffmpeg.run(
			'-i',
			name,
			'-row-mt',
			'1',
			'-r',
			'30',
			'-t',
			'2.99',
			'-an',
			'-c:v',
			'libvpx-vp9',
			'-pix_fmt',
			'yuva420p',
			'-vf',
			'scale=512:512',
			'-b:v',
			'256k',
			'output.webm'
		);
		const data = ffmpeg.FS('readFile', 'output.webm');
		const blob = new Blob([data.buffer], { type: 'video/webm' });
		// const video = window.document.querySelector('video#player');

		// const source = video?.querySelector('source');
		// const url = window.URL.createObjectURL(blob);

		// source.setAttribute('src', url);
		// video.load();
		// video
		// 	.play()
		// 	.then(
		// 		(ful) => console.log(ful),
		// 		(rej) => console.log(rej)
		// 	)
		// 	.catch((err) => console.log(err));
		// clickFunction(url);
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
		<div class="title-bar-text">텔레그램 스티커 생성기</div>
		<div class="title-bar-controls">
			<button aria-label="Minimize" />
			<button aria-label="Maximize" />
			<button aria-label="Close" />
		</div>
	</div>
	<div class="window-body" style="overflow:hidden;">
		<menu role="tablist">
			<!-- <li role="tab" aria-selected="false"><a href="#tabs">직접업로드</a></li> -->
			<li role="tab" aria-selected="true"><a href="#tabs">아카콘</a></li>
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
					<li>움직이는 스티커의 경우 256kb가 최대입니다.</li>
				</ul>
			</div>
		</div>

		<br />

		<div class="field-row">
			<label for="text17">아카콘 번호</label>
			<input id="text17" type="text" />
		</div>

		<br />

		<div class="field-row" style="width: 100%">
			<label for="range26">품질</label>
			<label for="range26">64k</label>
			<input id="range26" type="range" min="1" max="11" value="5" />
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

		<input type="file" id="uploader" />
		<div style="display: flex; width: 100%;">
			<input style="flex:1;" type="submit" />
			<input style="flex:1;" type="reset" />
		</div>

		<br />

		<div class="field-row-stacked" style="width: 100%">
			<label for="text19">인코딩 로그</label>
			<textarea id="text19" rows="8" value="" readonly />
		</div>

		<br />

		hihi
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
			<label for="text20">@misa-alasa 신규 스티커 작성 코드</label>
			<textarea
				id="text20"
				rows="8"
				value="/newvideo 12351235,43623462346,54856856,13452345234,632346234,12351253"
			/>
		</div>
		<div class="field-row-stacked" style="width: 100%">
			<label for="text20">@misa-alasa 스티커 추가 작성 코드</label>
			<textarea
				id="text21"
				rows="8"
				value="/addsticker misa-alasa-arca-1234 12351235,43623462346,54856856,13452345234,632346234,12351253"
			/>
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
