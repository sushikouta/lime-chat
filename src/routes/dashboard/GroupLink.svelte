<script lang="ts">
    export let title
    export let lastMessage
</script>

<div class="bg-zinc-800 w-auto p-[20px] rounded-[10px]">
    <div class="flex">
        <p class="font-bold text-xl mb-[10px]">{title}</p>
        <div class="ml-auto flex text-zinc-400">
            <span class="icon my-[2px] mr-[5px]"><i class="fas fa-comment-alt"></i></span>
            <p class="translate-y-[1px]">100</p>
        </div>
    </div>
    <p class="ml-auto text-zinc-400 text-ellipsis whitespace-nowrap overflow-hidden mb-[10px]"><b>[{lastMessage.timestamp}] {lastMessage.author}</b>: {lastMessage.text}</p>
    <div class="flex">
        <span class="icon has-text-success"><i class="far fa-dot-circle"></i></span>
        <p class="font-semibold ml-[5px]">0</p>
    </div>
</div>