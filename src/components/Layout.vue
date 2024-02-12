

<template>
    <div class="bg-gray-800 text-white">
        <nav class="max-w-6xl mx-auto px-4 py-4 flex justify-between">
            <div class="flex space-x-4">

                <div>
                    <a href="https://www.kaizntree.com/" target="_blank" class="text-lg font-semibold">Kaizntree</a>
                </div>


            </div>

            <div>
                <button @click="logout"
                    class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded">Logout</button>
            </div>
        </nav>


    </div>
    <slot></slot>
</template>

<script>
import { useRouter } from 'vue-router';

export default {
    setup() {
        const router = useRouter();

        const logout = () => {

            localStorage.removeItem('token');
            localStorage.removeItem('refreshToken');

            router.push('/login');
        };

        return {
            logout,
        };
    },
};
</script>