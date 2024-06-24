<script lang="ts">
    import axios from "axios";

    const databeseURL = "https://script.google.com/macros/s/AKfycbz4UbocwKYQvDSnnVE01cu0vVp5ZxtQ9U4HeWJJQgLPAUK9tSFyAf0yCyDxSc0kyC7C/exec"

    let isLoading = false
    let errorMessage = ""
    let errorVisible = false

    let username = ""
    let password = ""

    function login() {
        errorVisible = false
        isLoading = true

        if (username == "") {
            errorMessage = "ユーザーネームが空です。"
            errorVisible = true
            return
        }
        if (password == "") {
            errorMessage = "パスワードが空です。"
            errorVisible = true
            return
        }
        
        axios.post(databeseURL, {
            data: {
                "request" : "SIGN_IN",
                "username": username,
                "password": password
            },
            headers: {
                "Content-Type": "application/json",
                "Access-Control-Allow-Origin": "https://script.google.com/"
            }
        })
            .then(function (response: any) {
                console.log(response.data)
            })
            .catch(function (error: any) {
                console.log(error)
            })
            .then(function () {
                isLoading = false
            })
    }
</script>

<div class="w-auto max-w-[400px] p-[20px] mx-auto rounded-[10px] bg-zinc-800 mt-[50vh] translate-y-[-50%]">
    <p class="text-2xl font-bold mb-[20px]">ログイン</p>

    <div class="input-container">
        <p class="text-base">ユーザーネーム</p>
        <input class="input is-small font-bold" bind:value={username}/>
    </div>
    <div class="input-container">
        <p class="text-base">パスワード</p>
        <input type="password" class="input is-small font-bold" bind:value={password}/>
    </div>
    <div class="flex mt-[20px]">
        <div class="flex" style="display: {errorVisible ? "flex" : "none"};">
            <span class="icon my-[3px] mr-[5px] has-text-danger"><i class="fas fa-exclamation-circle"></i></span>
            <p class="translate-y-[3px] has-text-danger" >{errorMessage}</p>
        </div>
        <button class="button is-link is-small ml-auto {isLoading ? "is-loading" : ""}" on:click={login}>ログイン</button>
    </div>
</div>

<style lang="scss">
    .input-container {
        display: flex;
        
        margin-bottom: 10px;

        input {
            width: 200px;

            margin-left: auto;
        }

        :last-of-type {
            margin-bottom: 0;
        }
    }
</style>