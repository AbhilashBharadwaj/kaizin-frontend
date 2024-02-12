<template>
    <div class="min-h-screen flex items-center justify-center bg-gray-100">
        <div class="max-w-md w-full space-y-8">
            <div>
                <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">
                    Sign in to your account
                </h2>
                <div v-if="loginError"
                    class="p-4 mb-4 text-sm text-red-700 bg-red-100 rounded-lg dark:bg-red-200 dark:text-red-800"
                    role="alert">
                    <span class="font-medium">Error!</span> {{ loginErrorMessage }}
                </div>
            </div>
            <form class="mt-8 space-y-6" @submit.prevent="handleLogin">
                <input type="hidden" name="remember" value="true">
                <div class="rounded-md shadow-sm -space-y-px">
                    <div>
                        <label for="username" class="sr-only">Username</label>
                        <input id="username" name="username" type="text" autocomplete="username" required
                            class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
                            placeholder="Username" v-model="credentials.username">
                    </div>
                    <div>
                        <label for="password" class="sr-only">Password</label>
                        <input id="password" name="password" type="password" autocomplete="current-password" required
                            class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
                            placeholder="Password" v-model="credentials.password">
                    </div>
                </div>

                <div>
                    <button type="submit"
                        class="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                        Sign in
                    </button>
                </div>
            </form>
        </div>
    </div>
</template>
  
<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();
const credentials = ref({
    username: '',
    password: ''
});

const loginError = ref(false);
const loginErrorMessage = ref('');

const handleLogin = async () => {
    try {
        const response = await fetch('https://kaizntree-dashboard-f671ec3afb12.herokuapp.com/api/token/', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(credentials.value)
        });

        if (!response.ok) {
            throw new Error(`Login failed: ${response.statusText}`);
        }

        const data = await response.json();
        localStorage.setItem('token', data.access);
        localStorage.setItem('refreshToken', data.refresh);

        router.push('/dashboard');
    } catch (error) {
        loginError.value = true;
        loginErrorMessage.value = error.message;
    }
};

</script>
