<script lang="ts">
    import Room from '$lib/Room.svelte'

    import { onMount } from 'svelte'
    import axios from 'axios'
    import cookies from 'js-cookie'
    import type { LoginData } from '$lib/database';

    let loaded = false

    let loginData: LoginData

    async function main() {
        let userId     = cookies.get('userId')
        let loginToken = cookies.get('loginToken')

        if (userId == undefined || loginToken == undefined) {
            window.location.href = '/account'
            return
        } else {
            let response: {
                'success': boolean,
                'message': string
            } = (await axios.get(`${import.meta.env.VITE_URL}/api/account/logged?userId=${userId}&loginToken=${loginToken}`)).data

            if (!response.success) {
                cookies.remove('userId')
                cookies.remove('loginToken')

                window.location.href = '/account'
            }
        } 

        loginData = {
            userId: userId,
            loginToken: loginToken
        }

        loaded = true
    }

    onMount(main)
</script>

{#if loaded}
    <div class="w-[100%] max-w-[max(800px,60%)] mx-[auto] p-[20px]">
        <Room loginData={loginData} roomId="729c41c4-e9e8-4144-af10-ceb06e8ae0be" />
        <Room loginData={loginData} roomId="b3b71367-c354-3062-0b46-22c5eba67477" />
    </div>
{:else}
    <div class="mt-[50vh] translate-y-[-50%] text-center text-xl">Now Loading...</div>
{/if}