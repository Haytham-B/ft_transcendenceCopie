<script lang="ts">
    import { onMount } from "svelte";
    import { hostname } from "../../lib/hostname"
    import { onDestroy } from "svelte";
	  import { createEventDispatcher } from 'svelte';
	  import { userId, jwt_cookie } from "$lib/stores";
	  import { goto } from "$app/navigation";

    // export let data;
    // export let isDFAActive;
    
	//   const dispatch = createEventDispatcher();

    let code = ""; // variable to store the user's 2FA code
  
    async function handleSubmit() {
      console.log(code);
      const url = `/api/auth/2fa/verify`;
      const params = {
          method: "POST",
          headers: {
              'Content-Type': 'application/json',
              'Authorization': `Bearer ${$jwt_cookie}`
          },
          body: JSON.stringify({
              code,
              id: $userId
          })
      }
      try{
        let response = await fetch(url, params);
        if (response.ok)
        {
            const value = await response.json();
            console.log("GOOD");
            goto("/app/dashboard");
              // if (res.status == 201) {
              //       console.log("ces good")
              //       isDFAActive = false;
              //       console.log("pas de catch");
              //   } else {
              //       console.log("pas good")
              //   }
              //   console.log(value.message)
        }
        else
        {
          goto("/");
        }
      }
      catch
      {
        console.error("fetch failed in 2fa");
        goto("/");
      }

          // .then(async (res) => {
              
          // })
          // .catch((err) => {
          //     console.log("ERROR: " + err);
          //     console.error("Le back ne repond pas")
          // })
  }

    onMount(() => {
      console.log('in Dfa');
      code = "";
    });

  </script>
  
  <main>
    <h1>Welcome to DFA Homepage!</h1>
    <form on:submit|preventDefault={handleSubmit}>
      <label for="code">Enter your 2FA code:</label>
      <input type="text" id="code" bind:value={code} />
      <button type="submit">Submit</button>
    </form>
  </main>
  
  <style>
    /* Add your beautiful and girly styles here */
    main {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 2rem;
    }
  
    h1 {
      font-family: "Arial", sans-serif;
      font-size: 2rem;
      color: pink;
      text-align: center;
      margin-bottom: 1rem;
    }
  
    form {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }
  
    label {
      font-family: "Arial", sans-serif;
      font-size: 1rem;
      color: purple;
      margin-bottom: 0.5rem;
    }
  
    input {
      padding: 0.5rem;
      border-radius: 5px;
      border: 1px solid purple;
      margin-bottom: 1rem;
    }
  
    button {
      padding: 0.5rem 1rem;
      border-radius: 5px;
      background-color: pink;
      color: white;
      font-family: "Arial", sans-serif;
      font-size: 1rem;
      border: none;
      cursor: pointer;
    }
  </style>