<script lang="ts">
	import { getAdminDetails } from '$lib/apis/auths';
	import { onMount, tick, getContext } from 'svelte';
	import { config } from '$lib/stores';
	const i18n = getContext('i18n');
	let adminDetails = null;
	onMount(async () => {
		adminDetails = await getAdminDetails(localStorage.token).catch((err) => {
			console.error(err);
			return null;
		});
	});
</script>

<div class="fixed inset-0 z-50 flex items-center justify-center p-4">
	<!-- Backdrop with modern blur effect -->
	<div class="absolute inset-0 bg-gradient-to-br from-slate-900/80 via-purple-900/40 to-slate-900/80 backdrop-blur-xl"></div>
	
	<!-- Main content container -->
	<div class="relative w-full max-w-lg">
		<!-- Animated background elements -->
		<div class="absolute -top-4 -left-4 w-24 h-24 bg-gradient-to-r from-blue-500/20 to-purple-500/20 rounded-full blur-xl animate-pulse"></div>
		<div class="absolute -bottom-4 -right-4 w-32 h-32 bg-gradient-to-r from-purple-500/20 to-pink-500/20 rounded-full blur-xl animate-pulse delay-1000"></div>
		
		<!-- Main card -->
		<div class="relative bg-white/95 dark:bg-gray-900/95 backdrop-blur-sm rounded-3xl shadow-2xl border border-white/20 dark:border-gray-700/50 overflow-hidden">
			<!-- Header with gradient -->
			<div class="bg-gradient-to-r from-blue-600 via-purple-600 to-indigo-600 p-8 text-center relative overflow-hidden">
				<!-- Decorative elements -->
				<div class="absolute top-0 left-0 w-full h-full opacity-10">
					<div class="absolute top-4 left-4 w-16 h-16 border-2 border-white rounded-full"></div>
					<div class="absolute bottom-4 right-4 w-12 h-12 border-2 border-white rounded-full"></div>
					<div class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 w-24 h-24 border border-white rounded-full"></div>
				</div>
				
				<!-- Status icon -->
				<div class="relative mb-4">
					<div class="w-16 h-16 mx-auto bg-white/20 rounded-full flex items-center justify-center backdrop-blur-sm">
						<svg class="w-8 h-8 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
							<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
						</svg>
					</div>
				</div>
				
				<!-- Title -->
				<h1 class="text-2xl font-bold text-white mb-2 leading-tight">
					{#if ($config?.ui?.pending_user_overlay_title ?? '').trim() !== ''}
						{$config.ui.pending_user_overlay_title}
					{:else}
						Account Activation Required
					{/if}
				</h1>
				<p class="text-blue-100 text-sm font-medium">
					Your AJUDA DIGITAL access is pending approval
				</p>
			</div>
			
			<!-- Content -->
			<div class="p-8 space-y-6">
				<!-- Description -->
				<div class="text-center">
					<p class="text-gray-600 dark:text-gray-300 leading-relaxed">
						{#if ($config?.ui?.pending_user_overlay_content ?? '').trim() !== ''}
							{$config.ui.pending_user_overlay_content}
						{:else}
							Your account is currently awaiting activation. Please contact the administrator to gain access to AJUDA DIGITAL services.
						{/if}
					</p>
				</div>
				
				<!-- WhatsApp Contact Card -->
				<div class="bg-gradient-to-r from-green-50 to-emerald-50 dark:from-green-900/20 dark:to-emerald-900/20 rounded-2xl p-6 border border-green-200/50 dark:border-green-700/50">
					<div class="text-center space-y-4">
						<div class="flex items-center justify-center space-x-2">
							<div class="w-8 h-8 bg-green-600 rounded-full flex items-center justify-center">
								<svg class="w-4 h-4 text-white" viewBox="0 0 24 24" fill="currentColor">
									<path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 01-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 01-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 012.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0012.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L.057 24l6.305-1.654a11.882 11.882 0 005.683 1.448h.005c6.554 0 11.89-5.335 11.893-11.893A11.821 11.821 0 0020.893 3.488"/>
								</svg>
							</div>
							<h3 class="font-semibold text-green-800 dark:text-green-200">Quick Activation</h3>
						</div>
						<p class="text-sm text-green-700 dark:text-green-300">
							Chat with admin to activate your account instantly
						</p>
						<a
							href="https://wa.me/67074735879"
							target="_blank"
							class="group inline-flex items-center gap-3 px-6 py-3 bg-gradient-to-r from-green-600 to-emerald-600 hover:from-green-700 hover:to-emerald-700 text-white font-medium rounded-xl transition-all duration-300 transform hover:scale-105 hover:shadow-lg"
						>
							<svg class="w-5 h-5 transition-transform group-hover:rotate-12" viewBox="0 0 24 24" fill="currentColor">
								<path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 01-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 01-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 012.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0012.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L.057 24l6.305-1.654a11.882 11.882 0 005.683 1.448h.005c6.554 0 11.89-5.335 11.893-11.893A11.821 11.821 0 0020.893 3.488"/>
							</svg>
							Chat with Admin
							<svg class="w-4 h-4 transition-transform group-hover:translate-x-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
								<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
							</svg>
						</a>
					</div>
				</div>
				
				<!-- Admin Details -->
				{#if adminDetails}
					<div class="bg-gray-50 dark:bg-gray-800/50 rounded-xl p-4 border border-gray-200 dark:border-gray-700">
						<div class="flex items-center space-x-3">
							<div class="w-10 h-10 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center">
								<svg class="w-5 h-5 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
									<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z" />
								</svg>
							</div>
							<div class="flex-1 min-w-0">
								<p class="text-sm font-medium text-gray-900 dark:text-white">{adminDetails.name}</p>
								<p class="text-sm text-gray-500 dark:text-gray-400 truncate">{adminDetails.email}</p>
							</div>
							<div class="text-xs bg-blue-100 dark:bg-blue-900/50 text-blue-800 dark:text-blue-200 px-2 py-1 rounded-full">
								Admin
							</div>
						</div>
					</div>
				{/if}
				
				<!-- Action Buttons -->
				<div class="flex flex-col space-y-3 pt-4">
					<button
						class="group flex items-center justify-center space-x-2 px-6 py-3 bg-gradient-to-r from-red-600 to-red-700 hover:from-red-700 hover:to-red-800 text-white font-medium rounded-xl transition-all duration-300 transform hover:scale-105 hover:shadow-lg"
						on:click={async () => {
							location.href = '/';
						}}
					>
						<svg class="w-4 h-4 transition-transform group-hover:rotate-180" fill="none" stroke="currentColor" viewBox="0 0 24 24">
							<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
						</svg>
						<span>{$i18n.t('Check Again')}</span>
					</button>
					
					<button
						class="text-sm text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200 transition-colors py-2 underline decoration-dotted underline-offset-4"
						on:click={async () => {
							localStorage.removeItem('token');
							location.href = '/auth';
						}}
					>
						{$i18n.t('Sign Out')}
					</button>
				</div>
			</div>
		</div>
	</div>
</div>