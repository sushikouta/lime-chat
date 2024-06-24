<script lang="ts">
    import "$lib/app.css"
</script>

<style lang="postcss">
    @import "https://cdn.jsdelivr.net/npm/bulma@1.0.0/css/bulma.min.css";

    :global(body) {
        color: white;

        height: 100%;

        margin: 0;
    }

    :global(html) {
        background-color: theme(colors.zinc.700);
    }
</style>

<main class="w-[auto] max-w-[1000px] mx-[auto]">
    <slot />
</main>