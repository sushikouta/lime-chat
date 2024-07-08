<script lang="ts">
    import MessageNode from '$lib/Message.svelte'
    import { onMount } from 'svelte'
    import axios from 'axios'
    import cookies from 'js-cookie'
    import { io, Socket } from 'socket.io-client'
    import type { Message } from '$lib/database'

    let userId: string | undefined
    let loginToken: string | undefined

    let loaded = false

    let errorMessage = ''
    let isError = false

    function throwError(message: string) {
        errorMessage = message
        isError = true
    }

    let socket: Socket

    let websocketLoading = false

    let messages: Message[] = []

    let messageText = ""

    let sending = false

    let notificationTime = 0
    let notificationCounter: NodeJS.Timeout
    let notificationCount = 0

    let focused = true

    async function main() {
        userId = cookies.get('userId')
        loginToken = cookies.get('loginToken')

        if (userId == undefined || loginToken == undefined) {
            window.location.href = '/account'
        } else {
            try {
                let response: {
                    'success': boolean,
                    'message': string
                } = (await axios.get(`${import.meta.env.VITE_URL}/api/account/logged?userId=${userId}&loginToken=${loginToken}`)).data

                if (!response.success) {
                    cookies.remove('userId')
                    cookies.remove('loginToken')

                    window.location.href = '/account'
                }
            } catch (e) {
                cookies.remove('userId')
                cookies.remove('loginToken')

                window.location.href = '/account'
            }
        } 

        loaded = true

        const url = new URL(decodeURIComponent(document.location.href))

        let roomId = url.searchParams.get('room')

        if (!roomId) {
            throwError('部屋が見つかりません。')
            return
        }

        websocketLoading = true

        socket = io(import.meta.env.VITE_URL)

        socket.on('connect', () => {
            socket.emit('join', JSON.stringify({
                'userId': userId,
                'loginToken': loginToken,
                'roomId': roomId
            }))
        })

        socket.on('init', message => {
            let data: {
                'success': boolean,
                'message': string,
                'messages': Message[]
            } = JSON.parse(message)

            if (data.success) {
                messages = [...data.messages.reverse()]
                websocketLoading = false
            } else {
                throwError(data.message)
                socket.close()
            }
        })

        window.onfocus = () => {
            focused = true
            notificationCount = 0
            document.title = 'Lime chat'
        }
        window.onblur = () => {
            focused = false
        }

        socket.on('message', message => {
            let data: Message = JSON.parse(message)

            messages = [...messages, data]

            if (userId == data.recipientId) {
                if (sending) {
                    messageText = ""

                    sending = false
                }
            }

            notificationTime = 1000
            
            if (!focused) {
                notificationCount++
                document.title = `[${notificationCount}] Lime chat`
            }

            if (notificationCounter) {
                clearInterval(notificationCounter)
            }
            notificationCounter = setInterval(() => {
                if (notificationTime-- == 0) {
                    clearInterval(notificationCounter)
                }
            }, 10)

            let notification = document.querySelector('#notification')! as HTMLElement
            let notificationSpace = document.querySelector('#notification-space')! as HTMLElement

            notificationSpace.style.height = (notification.clientHeight + 10) + 'px'
        })
    }

    onMount(main)

    async function send() {
        sending = true

        socket.emit('send', JSON.stringify({
            userId: userId,
            loginToken: loginToken,
            contentText: messageText
        }))
    }
</script>

{#if loaded}
    {#if isError}
        <div class="flex has-text-danger translate-y-[50vh] mx-[auto] items-center justify-center">
            <div class="icon mr-[10px] my-[auto]">
                <i class="fa-solid fa-circle-exclamation"></i>
            </div>
            <p class="my-[auto]">{errorMessage}</p>
        </div>
    {:else}
        <div class="h-[100%] p-[20px]">
            <div class="overflow-auto h-[calc(100%-40px)]" id="message-container">
                {#if websocketLoading}
                    <div class="translate-y-[calc(50vh-20px)] text-center text-xl">接続中...</div>
                {:else}
                    {#if messages.length}
                        <div class="fixed max-w-[940px] w-[calc(100%-60px)] px-[15px] py-[10px] bg-white rounded-[10px] shadow-md translate-x-[10px]" class:visibility-hidden={notificationTime == 0}  id="notification">
                            <p class="has-text-primary mb-[5px] font-bold">最新のメッセージ</p>
                            <div class="ml-[10px] mb-[10px]">
                                <MessageNode content={messages[messages.length - 1]} />
                            </div>
                            <progress class="progress is-small is-primary mb-[5px]" value={notificationTime} max="1000" />
                        </div>
                        <div class="w-[auto]" id="notification-space"></div>
                        {#each messages as message}
                            <div class="mb-[10px]">
                                <MessageNode content={message} />
                            </div>
                        {/each}
                    {:else}
                        <p>まだ誰も発言していません。</p>
                    {/if}
                {/if}
            </div>
            <div class="flex mt-[10px]">
                <input type="text" class="input is-small mr-[10px] font-bold" bind:value={messageText} disabled={websocketLoading || sending} />
                <button class="button is-link is-small" on:click={send} disabled={websocketLoading || sending}>送信</button>
            </div>
        </div>
    {/if}
{:else}
    <div class="translate-y-[50vh] text-center text-xl">Now Loading...</div>
{/if}

<style lang="scss">
    .visibility-hidden {
        visibility: hidden;
    }
</style>