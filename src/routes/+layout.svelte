<script lang='ts'>
    import '../app.css'
    import 'bulma/css/bulma.css'
    import '@fortawesome/fontawesome-free/css/all.min.css'
</script>

<svelte:head>
    <title>Lime chat</title>
</svelte:head>

<div class='w-[100%] h-[100vh]'>
    <main class='max-w-[1000px] h-[100%] mx-[auto]'>
        <slot />
    </main>
</div>