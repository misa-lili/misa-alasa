<script lang="ts">
	import { onMount } from 'svelte';

	onMount(() => {
		const menu = document.querySelector('menu') as HTMLMenuElement;
		for (let i = 0; i < menu.children.length; i++) {
			const item = menu.children.item(i);
			if (item.firstChild?.textContent === '수동') {
				item?.setAttribute('aria-selected', 'true');
			} else {
				item?.setAttribute('aria-selected', 'false');
			}
		}
	});
</script>

<p>어디 한번 잘 해보세요!</p>
<ul>
	<li>스티커 셋의 최대 크기는 50개 입니다.</li>
	<li>품질을 조절해서 스티커의 용량을 조절해주세요.</li>
	<li>스티커의 용량은 256kb가 최대입니다.</li>
	<li>변환 후 다운로드를 해주세요.</li>
	<li>텔레그램에서 @Stickers 봇과 대화를 시작하세요.</li>
	<li>봇에게 /newvideo 명령어를 실행한 후, 변환하고 다운받은 .webm 파일을 파일 전송하세요.</li>
	<li>파일을 전송 한 후 적절한 이모티콘을 입력하세요.</li>
	<li>원하시는 만큼 반복하세요. 50개까지 가능합니다.</li>
	<li>파일의 비율은 1:1 이 아닐경우 찌그러질 수 있습니다.</li>
	<li>
		동영상일 경우 텔레그램은 3초가 최대이기 때문에 3초가 넘는 영상은 속도가 빨라지므로 미리
		잘라내야합니다.
	</li>
</ul>

<input type="file" id="uploader" accept="image/*,video/*" multiple />
