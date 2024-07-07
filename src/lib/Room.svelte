<script lang="ts">
    import type { LoginData } from "./database";

    export let loginData: LoginData
    export let roomId: string
</script>


<a href="/talk?room={roomId}" class="text-[rgb(64,70,84)]">
    <div class="border-[2px] rounded-[10px] px-[20px] py-[10px]">
        <h1 class="text-2xl font-bold">チャットるーむ</h1>
        <div class="flex">
            
        </div>
    </div>
</a>