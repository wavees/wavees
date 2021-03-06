<script>
  // import
  import { api, general } from "../../config/stores/global.js";
  import { user } from "../../config/stores/user.js";

  // Cookie manager
  import Cookie from "cookie-universal";
  const cookies = Cookie();

  import axios from "axios";

  // Importing components
  import Spinner from "../Loading/Spinner.svelte";
  import UserAvatar from "../Avatar/UserAvatar.svelte";

  import TransparentButton from "../Buttons/TransparentButton.svelte";
  import RoundedButton from "../Buttons/RoundedButton.svelte";

  // Icons
  import ChevronIcon from "../../icons/Chevron.svelte";

  // Let's subscribe to user store and
  // check if it's loaded.
  user.subscribe((value) => {
    if (value.loaded) {
      loading = false;
    }
  });

  // login
  // This function will try to login
  // this user.
  function login() {
    loading = true;

    window.location.href = "https://account.wavees.co.vu/authorize/@wavees";
  };

  // Should we show dropdown menu?
  let showDropdown = false;

  // Loading indicator
  let loading = true;
</script>

<div class="relative">
  <!-- 
    Let's show loading indicator
    if we still gathering information
    about user
   -->
  {#if loading}
    <Spinner size="25" />
  { :else }

    <!-- 
      Let's check if current user is
      exists (If user is logged in)
     -->
    { #if $user.current.token != null }
      <!-- 
        User is logged in, so let's show
        User Line
       -->
      <div class="flex items-center">
        <UserAvatar size="2.5" avatar={$user.current.avatar} username={$user.current.username} />

        <!-- Username & email -->
        <div class="mx-4">
          <p>{$user.current.username}</p>
          <p class="text-sm text-gray-600">{$user.current.email}</p>
        </div>

        <!-- Settings -->
        <button on:click={(e) => {
          if (showDropdown) {
            showDropdown = false;
          } else {
            showDropdown = true;
          }
        }} class="mx-4 rounded-lg hover:bg-gray-200 p-3 hover:shadow-2xl">
          {#if showDropdown}
            <ChevronIcon size="1" direction="down" />
          { :else }
            { #if $user.profiles.length > 1 }
              <img style="width: 1.2em; height: 1.2em;" src="./icons/users.svg" alt="Users icon">
            { :else }
              <img style="width: 1.2em; height: 1.2em;" src="./icons/user.svg" alt="User icon">
            { /if }
          {/if}
        </button>
      </div>
    { :else }
      <!-- 
        There isn't current user object in user store.
        So let's check if user need to choose account
        or if user isn't even logged in
       -->
      { #if $user.error == "ChooseAccount" }
        <div>
          <p>Выбрать аккаунт</p>
        </div>
      { :else }
        <div>
          <RoundedButton on:click={(e) => {
            login();
          }}>
            🔑 Авторизоваться
          </RoundedButton>
        </div>
      { /if }
    { /if }

  {/if}

  { #if showDropdown }
    <div style="z-index: 3;" class="absolute right-0 mt-2 mr-6 w-full max-w-md py-6 px-4 bg-white rounded-lg shadow-xl flex flex-col justify-center">
      <div class="text-center">
        <h1 class="text-xl font-semibold">Аккаунты</h1>
    
        <!-- 
          Accounts list
          Here we'll list all user accounts
          -->

        <div class="flex flex-col items-center justify-center mt-2">
          {#each $user.profiles as profile}
            <div style="cursor: pointer" class="my-1 items-center hover:bg-gray-200 {$user.current.token == profile.token ? "bg-gray-200 border-solid border-blue-600 border-2" : "bg-white"} border-solid border-white border-2 w-full flex justify-start py-4 px-4 rounded-lg">
              <div class="flex">
                <UserAvatar avatar={profile.avatar} username={profile.username} />
                
                <div class="ml-4 text-left">
                  <h1 class="text-semibold">{profile.username.slice(0, 20)}{profile.username.split('').length > 20 ? "..." : ""}</h1>
                  <p class="text-xs text-gray-700">{profile.email}</p>
                </div>
              </div>
            </div>
          {/each}

          <div on:click={(e) => {
            window.location.href = `https://account.wavees.co.vu/authorize/add?return=@wavees.co.vu`;
          }} style="cursor: pointer" class="my-4 bg-gray-100 hover:bg-gray-300 bg-white w-full flex justify-start py-4 px-4 md:px-8 rounded-lg">
            <div class="flex">
              <img style="width: 2.5em; height: 2.5em;" class="mt-1" src="./icons/plus.svg" alt="Add icon">
              
              <div class="ml-4">
                <h1 class="text-semibold">Добавить аккаунт</h1>
                <p class="text-xs">Да-да, вы можете иметь несколько аккаунтов.</p>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 
        Fast settings
        -->
      <div class="{ $user.profiles.length > 1 ? "mt-4" : "" } text-center">
        <h1 class="text-xl font-semibold">Настройки</h1>

        <div class="w-full flex justify-center mt-6">
          <TransparentButton classes="mx-4" on:click={(e) => {
            cookies.remove('_account_token');
            user.clearStore();

            showDropdown = false;
          }}>
            Выйти
          </TransparentButton>

          <RoundedButton classes="mx-4" on:click={(e) => {
            window.location.href = "https://account.wavees.co.vu/settings?return=@wavees.co.vu";
          }}>
            Настройки
          </RoundedButton>
        </div>
      </div>
    </div>
  { /if }
</div>