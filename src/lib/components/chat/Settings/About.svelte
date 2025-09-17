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
	<div class=" space-y-4 overflow-y-scroll max-h-[28rem] lg:max-h-full">
		
		<!-- AJUDA DIGITAL Project Info -->
		<div class="bg-gradient-to-r from-red-50 to-yellow-50 dark:from-red-900/20 dark:to-yellow-900/20 rounded-xl p-6 border border-red-200 dark:border-red-800">
			<div class="flex items-center gap-3 mb-4">
				<div class="w-12 h-12 bg-gradient-to-br from-red-600 to-yellow-600 rounded-lg flex items-center justify-center">
					<span class="text-white font-bold text-lg">AD</span>
				</div>
				<div>
					<h2 class="text-xl font-bold text-gray-800 dark:text-white">AJUDA DIGITAL</h2>
					<p class="text-sm text-gray-600 dark:text-gray-300">Homegrown Government Chatbot by Timorese Youth</p>
				</div>
			</div>
			
			<div class="space-y-3 text-sm">
				<div class="flex items-center gap-2">
					<svg class="w-4 h-4 text-blue-600" fill="currentColor" viewBox="0 0 20 20">
						<path fill-rule="evenodd" d="M4.083 9h1.946c.089-1.546.383-2.97.837-4.118A6.004 6.004 0 004.083 9zM10 2a8 8 0 100 16 8 8 0 000-16zm0 2c-.076 0-.232.032-.465.262-.238.234-.497.623-.737 1.182-.389.907-.673 2.142-.766 3.556h3.936c-.093-1.414-.377-2.649-.766-3.556-.24-.56-.5-.948-.737-1.182C10.232 4.032 10.076 4 10 4zm3.971 5c-.089-1.546-.383-2.97-.837-4.118A6.004 6.004 0 0115.917 9h-1.946zm-2.003 2H8.032c.093 1.414.377 2.649.766 3.556.24.56.5.948.737 1.182.233.23.389.262.465.262.076 0 .232-.032.465-.262.238-.234.498-.623.737-1.182.389-.907.673-2.142.766-3.556zm1.166 4.118c.454-1.147.748-2.572.837-4.118h1.946a6.004 6.004 0 01-2.783 4.118zm-6.268 0C6.412 13.97 6.118 12.546 6.03 11H4.083a6.004 6.004 0 002.783 4.118z" clip-rule="evenodd"/>
					</svg>
					<span class="text-gray-700 dark:text-gray-300">Website:</span>
					<a href="https://www.ajuda-digital.com" target="_blank" class="text-blue-600 hover:text-blue-800 dark:text-blue-400 font-medium">
						www.ajuda-digital.com
					</a>
				</div>
				
				<div class="flex items-center gap-2">
					<svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
						<path fill-rule="evenodd" d="M12.316 3.051a1 1 0 01.633 1.265l-4 12a1 1 0 11-1.898-.632l4-12a1 1 0 011.265-.633zM5.707 6.293a1 1 0 010 1.414L3.414 10l2.293 2.293a1 1 0 11-1.414 1.414l-3-3a1 1 0 010-1.414l3-3a1 1 0 011.414 0zm8.586 0a1 1 0 011.414 0l3 3a1 1 0 010 1.414l-3 3a1 1 0 11-1.414-1.414L16.586 10l-2.293-2.293a1 1 0 010-1.414z" clip-rule="evenodd"/>
					</svg>
					<span class="text-gray-700 dark:text-gray-300">Repository:</span>
					<a href="https://github.com/ajitonelsonn/ajuda_digital" target="_blank" class="text-blue-600 hover:text-blue-800 dark:text-blue-400 font-medium">
						GitHub Project
					</a>
				</div>
				
				<div class="mt-4 p-3 bg-white/50 dark:bg-black/20 rounded-lg">
					<p class="text-xs text-gray-600 dark:text-gray-400 leading-relaxed">
						<strong>Mission:</strong> Democratizing access to government services in Timor-Leste through AI-powered multilingual assistants. 
						Our platform bridges the digital divide by providing citizens with instant access to complex bureaucratic information in English, Tetum, Portuguese, and Other Language.
					</p>
				</div>
			</div>
		</div>

		<!-- Development Team -->
		<div class="bg-gradient-to-r from-blue-50 to-indigo-50 dark:from-blue-900/20 dark:to-indigo-900/20 rounded-xl p-6 border border-blue-200 dark:border-blue-800">
			<h3 class="text-lg font-semibold text-gray-800 dark:text-white mb-4 flex items-center gap-2">
				<svg class="w-5 h-5 text-blue-600" fill="currentColor" viewBox="0 0 20 20">
					<path d="M13 6a3 3 0 11-6 0 3 3 0 016 0zM18 8a2 2 0 11-4 0 2 2 0 014 0zM14 15a4 4 0 00-8 0v3h8v-3z"/>
				</svg>
				Ajuda Digital Team
			</h3>
			
			<div class="grid gap-4">
				<!-- Team Member 1 -->
				<div class="flex items-center gap-3 p-3 bg-white/60 dark:bg-black/20 rounded-lg">
					<div class="w-10 h-10 bg-gradient-to-br from-red-500 to-orange-500 rounded-full flex items-center justify-center">
						<span class="text-white font-semibold text-sm">AN</span>
					</div>
					<div class="flex-1">
						<h4 class="font-semibold text-gray-800 dark:text-white text-sm">Ajito Nelson Lucio da Costa</h4>
						<p class="text-xs text-gray-600 dark:text-gray-400">Team Leader</p>
					</div>
					<a href="https://www.linkedin.com/in/ajitonelson/" target="_blank" class="text-blue-600 hover:text-blue-800 transition-colors">
						<svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
							<path fill-rule="evenodd" d="M16.338 16.338H13.67V12.16c0-.995-.017-2.277-1.387-2.277-1.39 0-1.601 1.086-1.601 2.207v4.248H8.014v-8.59h2.559v1.174h.037c.356-.675 1.227-1.387 2.526-1.387 2.703 0 3.203 1.778 3.203 4.092v4.711zM5.005 6.575a1.548 1.548 0 11-.003-3.096 1.548 1.548 0 01.003 3.096zm-1.337 9.763H6.34v-8.59H3.667v8.59zM17.668 1H2.328C1.595 1 1 1.581 1 2.298v15.403C1 18.418 1.595 19 2.328 19h15.34c.734 0 1.332-.582 1.332-1.299V2.298C19 1.581 18.402 1 17.668 1z" clip-rule="evenodd"/>
						</svg>
					</a>
				</div>

				<!-- Team Member 2 -->
				<div class="flex items-center gap-3 p-3 bg-white/60 dark:bg-black/20 rounded-lg">
					<div class="w-10 h-10 bg-gradient-to-br from-purple-500 to-pink-500 rounded-full flex items-center justify-center">
						<span class="text-white font-semibold text-sm">JS</span>
					</div>
					<div class="flex-1">
						<h4 class="font-semibold text-gray-800 dark:text-white text-sm">Jedinilda S. Seixas dos Reis</h4>
						<p class="text-xs text-gray-600 dark:text-gray-400">Team Member</p>
					</div>
					<a href="https://www.linkedin.com/in/jedinildadosreis/" target="_blank" class="text-blue-600 hover:text-blue-800 transition-colors">
						<svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
							<path fill-rule="evenodd" d="M16.338 16.338H13.67V12.16c0-.995-.017-2.277-1.387-2.277-1.39 0-1.601 1.086-1.601 2.207v4.248H8.014v-8.59h2.559v1.174h.037c.356-.675 1.227-1.387 2.526-1.387 2.703 0 3.203 1.778 3.203 4.092v4.711zM5.005 6.575a1.548 1.548 0 11-.003-3.096 1.548 1.548 0 01.003 3.096zm-1.337 9.763H6.34v-8.59H3.667v8.59zM17.668 1H2.328C1.595 1 1 1.581 1 2.298v15.403C1 18.418 1.595 19 2.328 19h15.34c.734 0 1.332-.582 1.332-1.299V2.298C19 1.581 18.402 1 17.668 1z" clip-rule="evenodd"/>
						</svg>
					</a>
				</div>

				<!-- Team Member 3 -->
				<div class="flex items-center gap-3 p-3 bg-white/60 dark:bg-black/20 rounded-lg">
					<div class="w-10 h-10 bg-gradient-to-br from-green-500 to-teal-500 rounded-full flex items-center justify-center">
						<span class="text-white font-semibold text-sm">AG</span>
					</div>
					<div class="flex-1">
						<h4 class="font-semibold text-gray-800 dark:text-white text-sm">Abrao Glorito DC</h4>
						<p class="text-xs text-gray-600 dark:text-gray-400">Team Member</p>
					</div>
					<a href="https://www.linkedin.com/in/abraoglorito/" target="_blank" class="text-blue-600 hover:text-blue-800 transition-colors">
						<svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
							<path fill-rule="evenodd" d="M16.338 16.338H13.67V12.16c0-.995-.017-2.277-1.387-2.277-1.39 0-1.601 1.086-1.601 2.207v4.248H8.014v-8.59h2.559v1.174h.037c.356-.675 1.227-1.387 2.526-1.387 2.703 0 3.203 1.778 3.203 4.092v4.711zM5.005 6.575a1.548 1.548 0 11-.003-3.096 1.548 1.548 0 01.003 3.096zm-1.337 9.763H6.34v-8.59H3.667v8.59zM17.668 1H2.328C1.595 1 1 1.581 1 2.298v15.403C1 18.418 1.595 19 2.328 19h15.34c.734 0 1.332-.582 1.332-1.299V2.298C19 1.581 18.402 1 17.668 1z" clip-rule="evenodd"/>
						</svg>
					</a>
				</div>
			</div>
		</div>

		<!-- Technical Information -->
		<div class="bg-gray-50 dark:bg-gray-800/50 rounded-xl p-4">
			<div class="mb-2.5 text-sm font-medium flex space-x-2 items-center">
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

						{#if $config?.features?.enable_version_update_check}
							<a
								href="https://github.com/open-webui/open-webui/releases/tag/v{version.latest}"
								target="_blank"
							>
								{updateAvailable === null
									? $i18n.t('Checking for updates...')
									: updateAvailable
										? `(v${version.latest} ${$i18n.t('available!')})`
										: $i18n.t('(latest)')}
							</a>
						{/if}
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

		<!-- Attribution Section -->
		<div class="bg-blue-50 dark:bg-blue-900/20 rounded-lg p-4 border border-blue-200 dark:border-blue-800">
			<h4 class="text-sm font-medium text-gray-800 dark:text-white mb-2 flex items-center gap-2">
				<svg class="w-4 h-4 text-blue-600" fill="currentColor" viewBox="0 0 20 20">
					<path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7-4a1 1 0 11-2 0 1 1 0 012 0zM9 9a1 1 0 000 2v3a1 1 0 001 1h1a1 1 0 100-2v-3a1 1 0 00-1-1H9z" clip-rule="evenodd"/>
				</svg>
				Built Upon Open WebUI
			</h4>
			<p class="text-xs text-gray-600 dark:text-gray-400 mb-3">
				AJUDA DIGITAL is powered by <a href="https://github.com/open-webui/open-webui" target="_blank" class="text-blue-600 hover:text-blue-800 font-medium">Open WebUI</a>, 
				an extensible, feature-rich web interface for LLM interactions. We've customized and enhanced it specifically for Timorese government services.
			</p>
			
			<div class="flex flex-wrap gap-2">
				<a href="https://discord.gg/5rJgQTnV4s" target="_blank" class="inline-block">
					<img
						alt="Discord"
						src="https://img.shields.io/badge/Discord-Open_WebUI-blue?logo=discord&logoColor=white"
						class="h-5"
					/>
				</a>

				<a href="https://twitter.com/OpenWebUI" target="_blank" class="inline-block">
					<img
						alt="X (formerly Twitter) Follow"
						src="https://img.shields.io/twitter/follow/OpenWebUI"
						class="h-5"
					/>
				</a>

				<a href="https://github.com/open-webui/open-webui" target="_blank" class="inline-block">
					<img
						alt="Github Repo"
						src="https://img.shields.io/github/stars/open-webui/open-webui?style=social&label=Star us on Github"
						class="h-5"
					/>
				</a>
			</div>
		</div>

		<!-- Technical Credits -->
		<div class="text-xs text-gray-400 dark:text-gray-500 space-y-2">
			<div>
				Emoji graphics provided by
				<a href="https://github.com/jdecked/twemoji" target="_blank" class="underline">Twemoji</a>, licensed under
				<a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" class="underline">CC-BY 4.0</a>.
			</div>
			
			<div>
				AI Models powered by <span class="font-medium">SEA-LION LLM</span> - Southeast Asia Large Language Model optimized for regional languages and contexts.
			</div>
		</div>

		<!-- Open WebUI License (Condensed) -->
		<details class="text-xs text-gray-400 dark:text-gray-500">
			<summary class="cursor-pointer font-medium mb-2 hover:text-gray-600 dark:hover:text-gray-300">
				📄 Open WebUI License Information
			</summary>
			<div class="mt-2 space-y-2 text-xs">
				<p>
					<strong>Created by:</strong>
					<a
						class="text-gray-500 dark:text-gray-300 font-medium underline"
						href="https://github.com/tjbck"
						target="_blank">Timothy J. Baek</a>
				</p>
				
				<p>Copyright (c) {new Date().getFullYear()} <a
					href="https://openwebui.com"
					target="_blank"
					class="underline">Open WebUI</a> - All rights reserved.</p>
				
				<p class="italic">
					This software is licensed under the BSD 3-Clause License with additional branding restrictions. 
					AJUDA DIGITAL is a derivative work that maintains proper attribution to the original Open WebUI project.
				</p>
				
				<p class="text-xs">
					<a href="https://github.com/open-webui/open-webui/blob/main/LICENSE" target="_blank" class="underline">
						View Full License
					</a>
				</p>
			</div>
		</details>

		<!-- AJUDA DIGITAL Footer -->
		<div class="text-center py-4 border-t border-gray-200 dark:border-gray-700">
			<div class="text-sm font-medium text-gray-800 dark:text-white">🇹🇱 Made with ❤️ in Timor-Leste</div>
			<div class="text-xs text-gray-500 dark:text-gray-400 mt-1">
				Empowering citizens through accessible government services
			</div>
		</div>

	</div>
</div>