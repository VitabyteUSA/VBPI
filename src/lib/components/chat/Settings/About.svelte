<script lang="ts">
	import { getVersionUpdates } from '$lib/apis';
	import { getOllamaVersion } from '$lib/apis/ollama';
	import { WEBUI_BUILD_HASH, WEBUI_VERSION } from '$lib/constants';
	import { WEBUI_NAME, config, showChangelog } from '$lib/stores';
	import { compareVersion } from '$lib/utils';
	import { onMount, getContext } from 'svelte';

	import Tooltip from '$lib/components/common/Tooltip.svelte';

	const i18n = getContext('i18n');

	let ollamaVersion = '';

	let updateAvailable = null;
	let version = {
		current: '',
		latest: ''
	};

	const checkForVersionUpdates = async () => {
		updateAvailable = null;
		version = await getVersionUpdates(localStorage.token).catch((error) => {
			return {
				current: WEBUI_VERSION,
				latest: WEBUI_VERSION
			};
		});

		console.log(version);

		updateAvailable = compareVersion(version.latest, version.current);
		console.log(updateAvailable);
	};

	onMount(async () => {
		ollamaVersion = await getOllamaVersion(localStorage.token).catch((error) => {
			return '';
		});

		if ($config?.features?.enable_version_update_check) {
			checkForVersionUpdates();
		}
	});
</script>

<div id="tab-about" class="flex flex-col h-full justify-between space-y-3 text-sm mb-6">
	<div class=" space-y-3 overflow-y-scroll max-h-[28rem] lg:max-h-full">
		<div>
			<div class=" mb-2.5 text-sm font-medium flex space-x-2 items-center">
				<div>
					{$WEBUI_NAME}
					{$i18n.t('Version')}
				</div>
			</div>
			<div class="flex w-full justify-between items-center">
				<div class="flex flex-col text-xs text-gray-700 dark:text-gray-200">
					<div class="flex gap-1">
						<Tooltip content={WEBUI_BUILD_HASH}>
							v{WEBUI_VERSION}
						</Tooltip>

					</div>

					<button
						class=" underline flex items-center space-x-1 text-xs text-gray-500 dark:text-gray-500"
						on:click={() => {
							showChangelog.set(true);
						}}
					>
						<div>{$i18n.t("See what's new")}</div>
					</button>
				</div>

				{#if $config?.features?.enable_version_update_check}
					<button
						class=" text-xs px-3 py-1.5 bg-gray-100 hover:bg-gray-200 dark:bg-gray-850 dark:hover:bg-gray-800 transition rounded-lg font-medium"
						on:click={() => {
							checkForVersionUpdates();
						}}
					>
						{$i18n.t('Check for updates')}
					</button>
				{/if}
			</div>
		</div>

		{#if ollamaVersion}
			<hr class=" border-gray-100 dark:border-gray-850" />

			<div>
				<div class=" mb-2.5 text-sm font-medium">{$i18n.t('Ollama Version')}</div>
				<div class="flex w-full">
					<div class="flex-1 text-xs text-gray-700 dark:text-gray-200">
						{ollamaVersion ?? 'N/A'}
					</div>
				</div>
			</div>
		{/if}

		<hr class=" border-gray-100 dark:border-gray-850" />

		{#if $config?.license_metadata}
			<div class="mb-2 text-xs">
				{#if !$WEBUI_NAME.includes('VBPi')}
					<span class=" text-gray-500 dark:text-gray-300 font-medium">{$WEBUI_NAME}</span> -
				{/if}

				<span class=" capitalize">{$config?.license_metadata?.type}</span> license purchased by
				<span class=" capitalize">{$config?.license_metadata?.organization_name}</span>
			</div>
		{:else}
			<div class="flex space-x-1">
			</div>
		{/if}

		<div class="mt-2 text-xs text-gray-400 dark:text-gray-500">
			VITABYTE VBPi End User License Agreement (EULA)

		</div>

		<div>
			<pre
				class="text-xs text-gray-400 dark:text-gray-500">Copyright (c) {new Date().getFullYear()} <a
					target="_blank"
					class="underline">Vitabyte (VBPi)</a
				>
All rights reserved.

This End User License Agreement ("Agreement") governs the use of the VBPi software (the "Software"). By installing, accessing, or using the Software, you ("Licensee" or "End User") agree to be bound by the terms of this Agreement. If you do not agree, do not install or use the Software.

 1.⁠ ⁠License Grant
Vitabyte grants Licensee a limited, non-exclusive, non-transferable, revocable license to install and use the Software solely for Licensee's internal business or personal use, subject to the restrictions in this Agreement.

 2.⁠ ⁠Ownership
The Software is licensed, not sold. Vitabyte and its licensors retain all rights, title, and interest in and to the Software, including all copyrights, trademarks, trade secrets, and other intellectual property.

 3.⁠ ⁠Restrictions
Licensee shall not, without Vitabyte's prior written consent:
•⁠  ⁠Copy, distribute, sublicense, sell, rent, lease, lend, or otherwise make the Software available to any third party.
•⁠  ⁠Modify, adapt, translate, or create derivative works of the Software.
•⁠  ⁠Reverse engineer, decompile, or disassemble the Software, except as expressly permitted by applicable law.
•⁠  ⁠Remove, alter, or obscure any copyright, trademark, or proprietary notices.
•⁠  ⁠Circumvent or attempt to disable any security, licensing, or technical protection features.

 4.⁠ ⁠Branding Restrictions
Licensee may not alter, obscure, or remove any "VBPi" branding, including the name, logo, or other identifiers, in any deployment or use of the Software. This restriction applies regardless of user count, except as explicitly provided in Section 5.

 5.⁠ ⁠Limited Exceptions for Enterprise Licensing
The branding restriction in Section 4 may be waived only under one of the following circumstances:
(i) Licensee has entered into a separate, duly executed Enterprise License Agreement with Vitabyte permitting branding modification; or
(ii) Licensee is an official contributor with a substantive code change accepted into Vitabyte's private codebase, and has received specific written permission from Vitabyte.
Absent such agreements, any removal or alteration of VBPi branding constitutes a material breach.

 6.⁠ ⁠Updates and Support
Vitabyte may, at its discretion, provide updates, patches, or new versions of the Software. Unless otherwise stated in a separate agreement, Vitabyte is not obligated to provide support or maintenance.

 7.⁠ ⁠Disclaimer of Warranties
THE SOFTWARE IS PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE, OR NON-INFRINGEMENT.

 8.⁠ ⁠Limitation of Liability
TO THE MAXIMUM EXTENT PERMITTED BY LAW, VITABYTE AND ITS LICENSORS SHALL NOT BE LIABLE FOR ANY INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES, OR FOR ANY LOSS OF PROFITS, DATA, OR BUSINESS INTERRUPTION, ARISING OUT OF OR RELATING TO THE SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGES.

 9.⁠ ⁠Termination
Vitabyte may terminate this Agreement immediately upon notice if Licensee breaches any provision. Upon termination, Licensee must cease all use and destroy all copies of the Software.

10.⁠ ⁠Governing Law
This Agreement shall be governed by and construed in accordance with the laws of [insert jurisdiction]. Any disputes shall be subject to the exclusive jurisdiction of the courts of [insert jurisdiction].

11.⁠ ⁠Entire Agreement
This Agreement constitutes the entire agreement between Licensee and Vitabyte regarding the Software and supersedes all prior agreements or understandings.
</pre>
		</div>

		<div class="mt-2 text-xs text-gray-400 dark:text-gray-500">
			{$i18n.t('Created by')}
			<a
				class=" text-gray-500 dark:text-gray-300 font-medium"
				href="https://github.com/tjbck"
				target="_blank">VITABYTE</a
			>
		</div>
	</div>
</div>
