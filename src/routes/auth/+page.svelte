<script>
	import DOMPurify from 'dompurify';
	import { marked } from 'marked';

	import { toast } from 'svelte-sonner';

	import { onMount, getContext, tick } from 'svelte';
	import { goto } from '$app/navigation';
	import { page } from '$app/stores';

	import { getBackendConfig } from '$lib/apis';
	import { ldapUserSignIn, getSessionUser, userSignIn, userSignUp } from '$lib/apis/auths';

	import { WEBUI_API_BASE_URL, WEBUI_BASE_URL } from '$lib/constants';
	import { WEBUI_NAME, config, user, socket } from '$lib/stores';

	import { generateInitialsImage, canvasPixelTest, querystringValue } from '$lib/utils';

	import Spinner from '$lib/components/common/Spinner.svelte';
	import OnBoarding from '$lib/components/OnBoarding.svelte';
	import SensitiveInput from '$lib/components/common/SensitiveInput.svelte';

	const i18n = getContext('i18n');

	let loaded = false;

	let mode = $config?.features.enable_ldap ? 'ldap' : 'signin';

	let form = null;

	let name = '';
	let email = 'demo@ajudadigital.com';
	let password = 'ajuda123A@';
	let confirmPassword = '';

	let ldapUsername = '';

	const setSessionUser = async (sessionUser) => {
		if (sessionUser) {
			console.log(sessionUser);
			toast.success($i18n.t(`You're now logged in.`));
			if (sessionUser.token) {
				localStorage.token = sessionUser.token;
			}
			$socket.emit('user-join', { auth: { token: sessionUser.token } });
			await user.set(sessionUser);
			await config.set(await getBackendConfig());

			const redirectPath = querystringValue('redirect') || '/';
			goto(redirectPath);
		}
	};

	const signInHandler = async () => {
		const sessionUser = await userSignIn(email, password).catch((error) => {
			toast.error(`${error}`);
			return null;
		});

		await setSessionUser(sessionUser);
	};

	const signUpHandler = async () => {
		if ($config?.features?.enable_signup_password_confirmation) {
			if (password !== confirmPassword) {
				toast.error($i18n.t('Passwords do not match.'));
				return;
			}
		}

		const sessionUser = await userSignUp(name, email, password, generateInitialsImage(name)).catch(
			(error) => {
				toast.error(`${error}`);
				return null;
			}
		);

		await setSessionUser(sessionUser);
	};

	const ldapSignInHandler = async () => {
		const sessionUser = await ldapUserSignIn(ldapUsername, password).catch((error) => {
			toast.error(`${error}`);
			return null;
		});
		await setSessionUser(sessionUser);
	};

	const submitHandler = async () => {
		if (mode === 'ldap') {
			await ldapSignInHandler();
		} else if (mode === 'signin') {
			await signInHandler();
		} else {
			await signUpHandler();
		}
	};

	const checkOauthCallback = async () => {
		// Get the value of the 'token' cookie
		function getCookie(name) {
			const match = document.cookie.match(
				new RegExp('(?:^|; )' + name.replace(/([.$?*|{}()[\]\\/+^])/g, '\\$1') + '=([^;]*)')
			);
			return match ? decodeURIComponent(match[1]) : null;
		}

		const token = getCookie('token');
		if (!token) {
			return;
		}

		const sessionUser = await getSessionUser(token).catch((error) => {
			toast.error(`${error}`);
			return null;
		});
		if (!sessionUser) {
			return;
		}

		localStorage.token = token;
		await setSessionUser(sessionUser);
	};

	let onboarding = false;

	async function setLogoImage() {
		await tick();
		const logo = document.getElementById('logo');

		if (logo) {
			const isDarkMode = document.documentElement.classList.contains('dark');

			if (isDarkMode) {
				const darkImage = new Image();
				darkImage.src = `${WEBUI_BASE_URL}/static/favicon-dark.png`;

				darkImage.onload = () => {
					logo.src = `${WEBUI_BASE_URL}/static/favicon-dark.png`;
					logo.style.filter = ''; // Ensure no inversion is applied if favicon-dark.png exists
				};

				darkImage.onerror = () => {
					logo.style.filter = 'invert(1)'; // Invert image if favicon-dark.png is missing
				};
			}
		}
	}

	onMount(async () => {
		if ($user !== undefined) {
			const redirectPath = $page.url.searchParams.get('redirect') || '/';
			goto(redirectPath);
		}
		await checkOauthCallback();

		form = $page.url.searchParams.get('form');

		loaded = true;
		setLogoImage();

		if (($config?.features.auth_trusted_header ?? false) || $config?.features.auth === false) {
			await signInHandler();
		} else {
			onboarding = $config?.onboarding ?? false;
		}
	});
</script>

<svelte:head>
	<title>
		AJUDA DIGITAL - Government Assistant
	</title>
</svelte:head>

<style>
	.tais-pattern {
		background: linear-gradient(135deg, 
			#1e293b 0%, 
			#334155 20%, 
			#dc2626 40%, 
			#f97316 60%, 
			#fbbf24 80%, 
			#fef3c7 100%
		);
		position: relative;
	}
	
	.tais-pattern::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-image: 
			repeating-linear-gradient(
				45deg,
				rgba(255,255,255,0.08) 0px,
				rgba(255,255,255,0.08) 2px,
				transparent 2px,
				transparent 15px
			),
			repeating-linear-gradient(
				-45deg,
				rgba(0,0,0,0.1) 0px,
				rgba(0,0,0,0.1) 1px,
				transparent 1px,
				transparent 10px
			);
	}

	.geometric-overlay {
		background-image: 
			radial-gradient(circle at 25% 75%, rgba(255,255,255,0.1) 2px, transparent 2px),
			radial-gradient(circle at 75% 25%, rgba(255,255,255,0.08) 1.5px, transparent 1.5px),
			radial-gradient(circle at 50% 50%, rgba(0,0,0,0.1) 1px, transparent 1px);
		background-size: 50px 50px, 35px 35px, 25px 25px;
	}

	.floating-animation {
		animation: float 8s ease-in-out infinite;
	}

	@keyframes float {
		0%, 100% { transform: translateY(0px) rotate(0deg); }
		25% { transform: translateY(-8px) rotate(1deg); }
		50% { transform: translateY(-4px) rotate(-1deg); }
		75% { transform: translateY(-12px) rotate(1deg); }
	}

	.pulse-glow {
		animation: pulse-glow 3s ease-in-out infinite;
	}

	@keyframes pulse-glow {
		0%, 100% { 
			opacity: 1; 
			transform: scale(1);
			box-shadow: 0 0 20px rgba(255,255,255,0.3);
		}
		50% { 
			opacity: 0.9; 
			transform: scale(1.05);
			box-shadow: 0 0 30px rgba(255,255,255,0.5);
		}
	}

	.shimmer {
		animation: shimmer 2s ease-in-out infinite;
	}

	@keyframes shimmer {
		0%, 100% { opacity: 0.8; }
		50% { opacity: 1; }
	}

	.slide-in-left {
		animation: slideInLeft 0.8s ease-out;
	}

	.slide-in-right {
		animation: slideInRight 0.8s ease-out;
	}

	@keyframes slideInLeft {
		0% { transform: translateX(-50px); opacity: 0; }
		100% { transform: translateX(0); opacity: 1; }
	}

	@keyframes slideInRight {
		0% { transform: translateX(50px); opacity: 0; }
		100% { transform: translateX(0); opacity: 1; }
	}

	.glass-morphism {
		backdrop-filter: blur(16px);
		background: rgba(255, 255, 255, 0.1);
		border: 1px solid rgba(255, 255, 255, 0.2);
	}

	.btn-glow {
		transition: all 0.3s ease;
	}

	.btn-glow:hover {
		transform: translateY(-2px);
		box-shadow: 0 10px 25px rgba(220, 38, 38, 0.4);
	}
</style>

<OnBoarding
	bind:show={onboarding}
	getStartedHandler={() => {
		onboarding = false;
		mode = $config?.features.enable_ldap ? 'ldap' : 'signup';
	}}
/>

<div class="w-full min-h-screen relative" id="auth-page">
	<div class="w-full h-full absolute top-0 left-0 bg-gradient-to-br from-slate-50 via-white to-slate-100 dark:from-slate-950 dark:via-slate-900 dark:to-slate-800"></div>

	<div class="w-full absolute top-0 left-0 right-0 h-8 drag-region" />

	{#if loaded}
		<div class="min-h-screen w-full flex font-primary z-50" id="auth-container">
			<!-- Left Side - Login Form -->
			<div class="w-full lg:w-1/2 flex flex-col justify-start py-8 px-8 sm:px-12 lg:px-16 bg-white dark:bg-slate-950 relative slide-in-left overflow-y-auto max-h-screen">
				<div class="w-full max-w-md mx-auto">
					{#if ($config?.features.auth_trusted_header ?? false) || $config?.features.auth === false}
						<div class="text-center">
							<div class="flex items-center justify-center gap-3 text-xl sm:text-2xl font-semibold text-slate-800 dark:text-slate-200 mb-4">
								<div>Signing in to AJUDA DIGITAL</div>
								<div><Spinner className="size-5" /></div>
							</div>
						</div>
					{:else}
						<!-- Back to Main Site Button -->
						<div class="mb-6">
							<a 
								href="https://www.ajuda-digital.com/" 
								target="_blank"
								class="inline-flex items-center gap-2 text-sm text-slate-600 dark:text-slate-400 hover:text-red-600 dark:hover:text-red-400 transition-colors group"
							>
								<svg class="w-4 h-4 transition-transform group-hover:-translate-x-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
									<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 19l-7-7m0 0l7-7m-7 7h18"/>
								</svg>
								Back to Main Website
							</a>
						</div>

						<div class="mb-8">
							<div class="flex items-center gap-4 mb-6">
								<div class="relative">
									<img
										id="logo"
										crossorigin="anonymous"
										src="{WEBUI_BASE_URL}/static/favicon.png"
										class="size-14 rounded-xl shadow-2xl ring-4 ring-red-500/20"
										alt="AJUDA DIGITAL Logo"
									/>
									<div class="absolute -top-1 -right-1 w-4 h-4 bg-green-500 rounded-full animate-pulse shadow-lg"></div>
								</div>
								<div>
									<h1 class="text-3xl font-bold bg-gradient-to-r from-red-600 via-red-500 to-orange-500 bg-clip-text text-transparent">AJUDA DIGITAL</h1>
									<p class="text-sm text-slate-600 dark:text-slate-300 font-medium">Government Assistant</p>
								</div>
							</div>
						</div>

						<form
							class="space-y-6"
							on:submit={(e) => {
								e.preventDefault();
								submitHandler();
							}}
						>
							<div class="text-center mb-6">
								<h2 class="text-2xl font-bold text-slate-800 dark:text-white mb-3">
									{#if $config?.onboarding ?? false}
										Get started with AJUDA DIGITAL
									{:else if mode === 'ldap'}
										Sign in with LDAP
									{:else if mode === 'signin'}
										Welcome Back
									{:else}
										Create Account
									{/if}
								</h2>
								<p class="text-sm text-slate-600 dark:text-slate-400 leading-relaxed">
									{mode === 'signin' 
										? 'Access your AI-powered government assistant' 
										: 'Create your account to get started'}
								</p>
							</div>

							{#if $config?.features.enable_login_form || $config?.features.enable_ldap || form}
								<div class="space-y-5">
									{#if mode === 'signup'}
										<div class="space-y-2">
											<label for="name" class="block text-sm font-semibold text-slate-700 dark:text-slate-300">
												Full Name
											</label>
											<input
												bind:value={name}
												type="text"
												id="name"
												class="w-full px-4 py-3.5 rounded-xl border border-slate-300 dark:border-slate-600 bg-white dark:bg-slate-800 text-slate-900 dark:text-white placeholder-slate-500 focus:ring-2 focus:ring-red-500 focus:border-transparent transition-all shadow-sm"
												autocomplete="name"
												placeholder="Enter your full name"
												required
											/>
										</div>
									{/if}

									{#if mode === 'ldap'}
										<div class="space-y-2">
											<label for="username" class="block text-sm font-semibold text-slate-700 dark:text-slate-300">
												Username
											</label>
											<input
												bind:value={ldapUsername}
												type="text"
												class="w-full px-4 py-3.5 rounded-xl border border-slate-300 dark:border-slate-600 bg-white dark:bg-slate-800 text-slate-900 dark:text-white placeholder-slate-500 focus:ring-2 focus:ring-red-500 focus:border-transparent transition-all shadow-sm"
												autocomplete="username"
												name="username"
												id="username"
												placeholder="Enter your username"
												required
											/>
										</div>
									{:else}
										<div class="space-y-2">
											<label for="email" class="block text-sm font-semibold text-slate-700 dark:text-slate-300">
												Email Address
											</label>
											<input
												bind:value={email}
												type="email"
												id="email"
												class="w-full px-4 py-3.5 rounded-xl border border-slate-300 dark:border-slate-600 bg-white dark:bg-slate-800 text-slate-900 dark:text-white placeholder-slate-500 focus:ring-2 focus:ring-red-500 focus:border-transparent transition-all shadow-sm"
												autocomplete="email"
												name="email"
												placeholder="your@email.com"
												required
											/>
										</div>
									{/if}

									<div class="space-y-2">
										<label for="password" class="block text-sm font-semibold text-slate-700 dark:text-slate-300">
											Password
										</label>
										<SensitiveInput
											bind:value={password}
											type="password"
											id="password"
											class="w-full px-4 py-3.5 rounded-xl border border-slate-300 dark:border-slate-600 bg-white dark:bg-slate-800 text-slate-900 dark:text-white placeholder-slate-500 focus:ring-2 focus:ring-red-500 focus:border-transparent transition-all shadow-sm"
											placeholder="Enter your password"
											autocomplete={mode === 'signup' ? 'new-password' : 'current-password'}
											name="password"
											required
										/>
									</div>

									{#if mode === 'signup' && $config?.features?.enable_signup_password_confirmation}
										<div class="space-y-2">
											<label for="confirm-password" class="block text-sm font-semibold text-slate-700 dark:text-slate-300">
												Confirm Password
											</label>
											<SensitiveInput
												bind:value={confirmPassword}
												type="password"
												id="confirm-password"
												class="w-full px-4 py-3.5 rounded-xl border border-slate-300 dark:border-slate-600 bg-white dark:bg-slate-800 text-slate-900 dark:text-white placeholder-slate-500 focus:ring-2 focus:ring-red-500 focus:border-transparent transition-all shadow-sm"
												placeholder="Confirm your password"
												autocomplete="new-password"
												name="confirm-password"
												required
											/>
										</div>
									{/if}
								</div>

								<div class="pt-6">
									{#if mode === 'ldap'}
										<button
											class="w-full bg-gradient-to-r from-red-600 via-red-500 to-red-600 hover:from-red-700 hover:via-red-600 hover:to-red-700 text-white font-semibold py-4 px-4 rounded-xl transition-all duration-300 transform hover:scale-[1.02] shadow-lg btn-glow"
											type="submit"
										>
											{$i18n.t('Authenticate')}
										</button>
									{:else}
										<button
											class="w-full bg-gradient-to-r from-red-600 via-red-500 to-red-600 hover:from-red-700 hover:via-red-600 hover:to-red-700 text-white font-semibold py-4 px-4 rounded-xl transition-all duration-300 transform hover:scale-[1.02] shadow-lg btn-glow"
											type="submit"
										>
											{mode === 'signin'
												? 'Sign In'
												: ($config?.onboarding ?? false)
													? $i18n.t('Create Admin Account')
													: 'Create Account'}
										</button>

										{#if $config?.features.enable_signup && !($config?.onboarding ?? false)}
											<div class="mt-8 text-center">
												<span class="text-sm text-slate-600 dark:text-slate-400">
													{mode === 'signin'
														? "Don't have an account?"
														: 'Already have an account?'}
												</span>
												<button
													class="ml-2 text-sm font-semibold text-red-600 hover:text-red-700 dark:text-red-400 dark:hover:text-red-300 transition-colors underline underline-offset-2"
													type="button"
													on:click={() => {
														if (mode === 'signin') {
															mode = 'signup';
														} else {
															mode = 'signin';
														}
													}}
												>
													{mode === 'signin' ? $i18n.t('Sign up') : $i18n.t('Sign in')}
												</button>
											</div>
										{/if}
									{/if}
								</div>
							{/if}

							{#if Object.keys($config?.oauth?.providers ?? {}).length > 0}
								<div class="inline-flex items-center justify-center w-full">
									<hr class="w-32 h-px my-4 border-0 dark:bg-gray-100/10 bg-gray-700/10" />
									{#if $config?.features.enable_login_form || $config?.features.enable_ldap || form}
										<span
											class="px-3 text-sm font-medium text-gray-900 dark:text-white bg-transparent"
											>{$i18n.t('or')}</span
										>
									{/if}

									<hr class="w-32 h-px my-4 border-0 dark:bg-gray-100/10 bg-gray-700/10" />
								</div>
								<div class="flex flex-col space-y-2">
									{#if $config?.oauth?.providers?.google}
										<button
											class="flex justify-center items-center bg-gray-700/5 hover:bg-gray-700/10 dark:bg-gray-100/5 dark:hover:bg-gray-100/10 dark:text-gray-300 dark:hover:text-white transition w-full rounded-full font-medium text-sm py-2.5"
											on:click={() => {
												window.location.href = `${WEBUI_BASE_URL}/oauth/google/login`;
											}}
										>
											<svg
												xmlns="http://www.w3.org/2000/svg"
												viewBox="0 0 48 48"
												class="size-6 mr-3"
											>
												<path
													fill="#EA4335"
													d="M24 9.5c3.54 0 6.71 1.22 9.21 3.6l6.85-6.85C35.9 2.38 30.47 0 24 0 14.62 0 6.51 5.38 2.56 13.22l7.98 6.19C12.43 13.72 17.74 9.5 24 9.5z"
												/><path
													fill="#4285F4"
													d="M46.98 24.55c0-1.57-.15-3.09-.38-4.55H24v9.02h12.94c-.58 2.96-2.26 5.48-4.78 7.18l7.73 6c4.51-4.18 7.09-10.36 7.09-17.65z"
												/><path
													fill="#FBBC05"
													d="M10.53 28.59c-.48-1.45-.76-2.99-.76-4.59s.27-3.14.76-4.59l-7.98-6.19C.92 16.46 0 20.12 0 24c0 3.88.92 7.54 2.56 10.78l7.97-6.19z"
												/><path
													fill="#34A853"
													d="M24 48c6.48 0 11.93-2.13 15.89-5.81l-7.73-6c-2.15 1.45-4.92 2.3-8.16 2.3-6.26 0-11.57-4.22-13.47-9.91l-7.98 6.19C6.51 42.62 14.62 48 24 48z"
												/><path fill="none" d="M0 0h48v48H0z" />
											</svg>
											<span>{$i18n.t('Continue with {{provider}}', { provider: 'Google' })}</span>
										</button>
									{/if}
									{#if $config?.oauth?.providers?.microsoft}
										<button
											class="flex justify-center items-center bg-gray-700/5 hover:bg-gray-700/10 dark:bg-gray-100/5 dark:hover:bg-gray-100/10 dark:text-gray-300 dark:hover:text-white transition w-full rounded-full font-medium text-sm py-2.5"
											on:click={() => {
												window.location.href = `${WEBUI_BASE_URL}/oauth/microsoft/login`;
											}}
										>
											<svg
												xmlns="http://www.w3.org/2000/svg"
												viewBox="0 0 21 21"
												class="size-6 mr-3"
											>
												<rect x="1" y="1" width="9" height="9" fill="#f25022" /><rect
													x="1"
													y="11"
													width="9"
													height="9"
													fill="#00a4ef"
												/><rect x="11" y="1" width="9" height="9" fill="#7fba00" /><rect
													x="11"
													y="11"
													width="9"
													height="9"
													fill="#ffb900"
												/>
											</svg>
											<span>{$i18n.t('Continue with {{provider}}', { provider: 'Microsoft' })}</span
											>
										</button>
									{/if}
									{#if $config?.oauth?.providers?.github}
										<button
											class="flex justify-center items-center bg-gray-700/5 hover:bg-gray-700/10 dark:bg-gray-100/5 dark:hover:bg-gray-100/10 dark:text-gray-300 dark:hover:text-white transition w-full rounded-full font-medium text-sm py-2.5"
											on:click={() => {
												window.location.href = `${WEBUI_BASE_URL}/oauth/github/login`;
											}}
										>
											<svg
												xmlns="http://www.w3.org/2000/svg"
												viewBox="0 0 24 24"
												class="size-6 mr-3"
											>
												<path
													fill="currentColor"
													d="M12 0C5.37 0 0 5.37 0 12c0 5.31 3.435 9.795 8.205 11.385.6.105.825-.255.825-.57 0-.285-.015-1.23-.015-2.235-3.015.555-3.795-.735-4.035-1.41-.135-.345-.72-1.41-1.23-1.695-.42-.225-1.02-.78-.015-.795.945-.015 1.62.87 1.845 1.23 1.08 1.815 2.805 1.305 3.495.99.105-.78.42-1.305.765-1.605-2.67-.3-5.46-1.335-5.46-5.925 0-1.305.465-2.385 1.23-3.225-.12-.3-.54-1.53.12-3.18 0 0 1.005-.315 3.3 1.23.96-.27 1.98-.405 3-.405s2.04.135 3 .405c2.295-1.56 3.3-1.23 3.3-1.23.66 1.65.24 2.88.12 3.18.765.84 1.23 1.92 1.23 3.225 0 4.605-2.805 5.625-5.475 5.925.435.375.81 1.095.81 2.22 0 1.605-.015 2.895-.015 3.3 0 .315.225.69.825.57C20.565 21.795 24 17.31 24 12c0-6.63-5.37-12-12-12z"
												/>
											</svg>
											<span>{$i18n.t('Continue with {{provider}}', { provider: 'GitHub' })}</span>
										</button>
									{/if}
									{#if $config?.oauth?.providers?.oidc}
										<button
											class="flex justify-center items-center bg-gray-700/5 hover:bg-gray-700/10 dark:bg-gray-100/5 dark:hover:bg-gray-100/10 dark:text-gray-300 dark:hover:text-white transition w-full rounded-full font-medium text-sm py-2.5"
											on:click={() => {
												window.location.href = `${WEBUI_BASE_URL}/oauth/oidc/login`;
											}}
										>
											<svg
												xmlns="http://www.w3.org/2000/svg"
												fill="none"
												viewBox="0 0 24 24"
												stroke-width="1.5"
												stroke="currentColor"
												class="size-6 mr-3"
											>
												<path
													stroke-linecap="round"
													stroke-linejoin="round"
													d="M15.75 5.25a3 3 0 0 1 3 3m3 0a6 6 0 0 1-7.029 5.912c-.563-.097-1.159.026-1.563.43L10.5 17.25H8.25v2.25H6v2.25H2.25v-2.818c0-.597.237-1.17.659-1.591l6.499-6.499c.404-.404.527-1 .43-1.563A6 6 0 1 1 21.75 8.25Z"
												/>
											</svg>

											<span
												>{$i18n.t('Continue with {{provider}}', {
													provider: $config?.oauth?.providers?.oidc ?? 'SSO'
												})}</span
											>
										</button>
									{/if}
								</div>
							{/if}

							{#if $config?.features.enable_ldap && $config?.features.enable_login_form}
								<div class="mt-2">
									<button
										class="flex justify-center items-center text-xs w-full text-center underline"
										type="button"
										on:click={() => {
											if (mode === 'ldap')
												mode = ($config?.onboarding ?? false) ? 'signup' : 'signin';
											else mode = 'ldap';
										}}
									>
										<span
											>{mode === 'ldap'
												? $i18n.t('Continue with Email')
												: $i18n.t('Continue with LDAP')}</span
										>
									</button>
								</div>
							{/if}
						</form>
					{/if}
				</div>
			</div>

			<!-- Right Side - App Information -->
			<div class="hidden lg:flex lg:w-1/2 tais-pattern geometric-overlay relative slide-in-right">
				<div class="absolute inset-0 bg-gradient-to-br from-black/30 via-black/20 to-black/40"></div>
				<div class="relative z-10 w-full h-full overflow-y-auto">
					<div class="flex flex-col justify-center items-center min-h-full p-8 text-white">
						<div class="max-w-md text-center space-y-8">
							<!-- Floating Logo -->
							<div class="floating-animation">
								<div class="w-24 h-24 glass-morphism rounded-2xl flex items-center justify-center pulse-glow mx-auto">
									<img
										src="{WEBUI_BASE_URL}/static/favicon.png"
										class="w-16 h-16 rounded-lg"
										alt="AJUDA DIGITAL"
									/>
								</div>
							</div>

							<div class="space-y-4">
								<h1 class="text-4xl font-bold bg-gradient-to-r from-white via-yellow-200 to-white bg-clip-text text-transparent">
									AJUDA DIGITAL
								</h1>
								<p class="text-lg text-white/90 font-medium">
									Homegrown Government Assistant
								</p>
								<p class="text-sm text-white/70">
									Built by Timorese Youth for Timor-Leste
								</p>
							</div>

							<!-- Service Highlight -->
							<div class="glass-morphism rounded-xl p-6 border border-white/20 space-y-4">
								<div class="flex items-center justify-center gap-2">
									<span class="text-2xl">🤖</span>
									<h2 class="text-lg font-bold text-yellow-200">AI-Powered Government Services</h2>
								</div>
								<p class="text-white/90 text-sm leading-relaxed">
									Access comprehensive government information and services through our intelligent assistant designed specifically for Timor-Leste.
								</p>
							</div>

							<!-- Feature Grid -->
							<div class="grid grid-cols-2 gap-3">
								<div class="glass-morphism rounded-lg p-4 border border-white/20 hover:bg-white/20 transition-all cursor-pointer">
									<div class="text-2xl mb-2">📋</div>
									<div class="text-xs font-semibold">10 Departments</div>
									<div class="text-xs text-white/80">Complete coverage</div>
								</div>
								<div class="glass-morphism rounded-lg p-4 border border-white/20 hover:bg-white/20 transition-all cursor-pointer">
									<div class="text-2xl mb-2">⚡</div>
									<div class="text-xs font-semibold">Instant Access</div>
									<div class="text-xs text-white/80">24/7 availability</div>
								</div>
							</div>

							<!-- Mission Statement -->
							<div class="glass-morphism border border-blue-400/30 rounded-lg p-4">
								<div class="flex items-center justify-center gap-2 mb-2">
									<span class="text-lg">🌟</span>
									<p class="text-sm font-semibold text-blue-200">
										Democratizing Government Access
									</p>
								</div>
								<p class="text-xs text-blue-100 leading-relaxed">
									Breaking down barriers between citizens and government services through innovative AI technology
								</p>
							</div>
						</div>

						<!-- Decorative Elements -->
						<div class="absolute top-12 left-8 w-16 h-16 border-2 border-white/20 rounded-full floating-animation" style="animation-delay: -3s;"></div>
						<div class="absolute bottom-16 right-8 w-14 h-14 border-2 border-yellow-400/30 rounded-lg rotate-45 floating-animation" style="animation-delay: -5s;"></div>
						<div class="absolute top-1/3 right-12 w-12 h-12 glass-morphism rounded-full floating-animation" style="animation-delay: -1.5s;"></div>
						<div class="absolute bottom-1/3 left-8 w-10 h-10 border-2 border-red-400/40 rounded-lg floating-animation" style="animation-delay: -4s;"></div>
					</div>
				</div>
			</div>
		</div>

		{#if $config?.metadata?.login_footer}
			<div class="fixed bottom-4 left-4 right-4 lg:left-auto lg:w-1/2 z-50">
				<div class="bg-white/90 dark:bg-slate-900/90 backdrop-blur-sm rounded-lg p-3 text-center lg:hidden">
					<div class="text-xs text-slate-600 dark:text-slate-400 marked">
						{@html DOMPurify.sanitize(marked($config?.metadata?.login_footer))}
					</div>
				</div>
			</div>
		{/if}
	{/if}
</div>