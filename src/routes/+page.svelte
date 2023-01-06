<script lang="ts">
    import SpotifyWebApi from "spotify-web-api-node";
    import {Data} from "../data.ts";
    import { page } from '$app/stores'

    let spotifyClientId = "6d4a73bb70b4458788399cb6da368b36"
    let spotifyClientSecret = "7a2c7e3e5b5640a39fab84ac273d792a"
    let spotifyRedirectUri = "http://localhost:9000"
    let scopes = ['user-read-private', 'user-read-email', 'user-library-read', 'playlist-read-private']
    let spotifyApi = new SpotifyWebApi({
        clientId: spotifyClientId,
        clientSecret: spotifyClientSecret,
        redirectUri: spotifyRedirectUri
    });
    let authUrl = spotifyApi.createAuthorizeURL(scopes, "spotify");

    // function onSpotifyClick() {
    //     console.log("Spotify auth clicked");
    // }

    let params = $page.url.searchParams
    let state = params.get("state");
    let spotifyAuthDone = false;
    // window.alert(state);
    if (state === "spotify") {
        let code = params.get("code");
        console.log("Spotify auth code: " + code);
        spotifyApi.authorizationCodeGrant(code).then(
            function (data) {
                console.log('The token expires in ' + data.body['expires_in']);
                console.log('The access token is ' + data.body['access_token']);
                console.log('The refresh token is ' + data.body['refresh_token']);

                // Set the access token on the API object to use it in later calls
                spotifyApi.setAccessToken(data.body['access_token']);
                spotifyApi.setRefreshToken(data.body['refresh_token']);
                Data.isSpotifyAuthenticated = true;
                spotifyAuthDone = true;
            },
            function (err) {
                console.log('Something went wrong!', err);
            }
        );
    }

    function onSpotifyClick() {
        Data.isSpotifyAuthenticated = true;
    }

    function onAppleClick() {
        console.log("Apple auth clicked");
        window.alert("hello")
    }
</script>

<main>
    <div class="flex flex-col font-epicfont justify-center text-center pt-4">
        <header class="fixed top-0 flex justify-center flex-col text-center w-full border-b border-gray-400 bg-white">
            <div class="text-5xl"><h1>DemenagerAuVerger</h1></div>
            <div class="text-xs"><h1>You must be a chad</h1></div>
        </header>
        <br><br>
        <br><br><br>
        <br><br>

        {#if spotifyAuthDone}
            <h1>Spotify Authenticated!</h1>
        {:else}
            <a href="{authUrl}">
                <button class="bg-blue-700 hover:bg-blue-500 text-white text-2xl p-5 m-7 rounded" on:click{onSpotifyClick}>Authenticate Spotify</button>
            </a>
        {/if}

        <button class="bg-blue-700 hover:bg-blue-500 text-white text-2xl p-5 m-7 mt-0 rounded" on:click={onAppleClick}>Authenticate Apple Music</button>


        <div class="text-xl lg:pl-40 lg:pr-40 lg:text-left">
            Move your spotify library to Apple Music.
            <div class="text-s"><a href="https://github.com/BoogieMonster1O1/DemenagerAuVerger" rel="noreferrer" target="_blank" class="text-blue-600">Source Code</a></div>
        </div>
    </div>
</main>
