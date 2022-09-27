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
  
  <div class="flex-center">
    <div aria-live="polite">
      <h1 class="header-login">Welcome to Task List Login</h1>
      <p class="description flex-center">Sign in via confirm link with your email below</p>
      <form class="form flex-center" on:submit|preventDefault={handleLogin}>
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
    .flex-center {
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .header-login {
      font-size: 5rem;
      font-family: "Fira sans", sans-serif;
      font-weight: 300;
      color: var(--gray);
    }
    .description {
      margin-bottom: 5em;
    }
    .inputField {
    background-color: var(--darker);
    padding: 1rem;
    border-radius: 1rem;
    margin-right: 1rem;
    color: var(--light);
    font-size: 1.25rem;
    margin-left: 0.5em;
    padding-right: 5em;
    }

  </style>