<script>
  // Let's now import some modules
  // and stores.
  import { stores, goto } from "@sapper/app"
  import axios from "axios";

  import { api } from "../../config/stores/global";

  import Cookie from "cookie-universal";

  // Cookie manager
  const cookies = Cookie();

  // Page store
  const { page } = stores();

  // Importing some components.
	import Spinner from "../../components/Loading/Spinner.svelte";

  import { onMount } from "svelte"

  // onMount function.
  // Here we'll check if this token
  // even exists or no.
  onMount(() => {
    // Firstly, let's check if this
    // token exists or not.
    const token = $page.params.token;

    axios.get(`${$api.url}/${$api.version}/account/${token}`)
    .then((response) => {
      // Let's now work with this
      // response...
      let data = response.data;

      if (data.type == "user" || data.type == "session") {
        // Okay, so now we need to create
        // token cookie and redirect user to main page...
        cookies.set('_account_token', token, {
          path: '/'
        });
        window.location.href = "/";
      } else {
        // This token doesn't exists.
        cookies.remove('_account_token');
        window.location.href = "/";
      }
    }).catch((error) => {
      cookies.remove('_account_token');
      window.location.href = "/";
    });
  });
</script>

<!-- 
  Main Layout
 -->
<div style="min-height: 100vh;" class="w-full h-full flex flex-col justify-center items-center">
  <!-- Spinner -->
	<div style="height: 100vh;" class="w-full flex justify-center items-center">
		<!-- Branding -->
		<div class="flex flex-col justify-center items-center">
			<div class="flex mb-6 items-center">
				<h1 style="font-family: Junegull" class="text-2xl text-bold">wavees</h1>
			</div>

			<Spinner size="25" />
		</div>
	</div>
</div>