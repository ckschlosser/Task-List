<script lang="ts">
    import { supabase } from "../supabaseClient";
  
    let loading = false
    let email = ''
  
    const handleLogin = async () => {
      try {
        loading = true
        const { error } = await supabase.auth.signInWithOtp({ email })
        if (error) throw error
        alert('Check your email for login link!')
      } catch (error) {
        if (error instanceof Error) {
          alert(error.message)
        }
      } finally {
        loading = false
      }
    }
  </script>
  
  <div class="">
    <div aria-live="polite">
      <h1 class="header-login center-text">Welcome to Task List Login</h1>
      <p class="description center-text">Sign in via confirm link with your email below</p>
      <form class="form flex flex-center" on:submit|preventDefault={handleLogin}>
        <div>
          <label for="email">Email</label>
          <input
            id="email"
            class="inputField"
            type="email"
            placeholder="Your email"
            bind:value={email}
          />
        </div>
        <div>
          <button type="submit" id="new-task-submit" aria-live="polite" disabled={loading}>
            <span>{loading ? 'Loading' : 'Confirm link'}</span>
          </button>
        </div>
      </form>
    </div>
  </div>

  <style>
     .flex {
    display: flex;
    flex-wrap: wrap;
  }
  .flex-center {
    justify-content: center;
    align-items: center;
  }
  .center-text {
    text-align: center;
  }
  
    .header-login {
      font-size: 2.5rem;
      font-family: "Fira sans", sans-serif;
      font-weight: 300;
      color: var(--gray);
      padding: 0 1rem;

    }
    .description {
      margin-bottom: 3em;
    }

    .inputField {
    background-color: var(--darker);
    padding: 0.5rem;
    border-radius: 1rem;
    margin-right: 1rem;
    color: var(--light);
    font-size: 1.25rem;
    margin-left: 0.5em;
    } 

    @media only screen and (min-width: 575px) {
        .header-login {
          font-size: 5rem;
        }
        .inputField {
          padding: 1rem;
        }
        .description {
          margin-bottom: 5rem;
        }

    }
  </style>