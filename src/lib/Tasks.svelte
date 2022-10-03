<script lang="ts">
    import { onMount } from 'svelte';
    import { supabase } from "../supabaseClient";
    import Auth from './Auth.svelte';

    let taskTextPlaceholder;
    let showSaveID;
    let task_text;
    let currentlyEditing = false;
    $: tasks = [];
    $: userData = {};
    
    
    const dataUpdate = async function() {
      let tempTasks = await supabase.from("Tasks").select('*');
      tasks = tempTasks.data

    }
   
    // Get tasks from supabase
    onMount(async () => {
		
        await dataUpdate();
        console.log('Tasks:', tasks);

        userData = await supabase.auth.getSession();

	  });

    async function handleOnSubmit() {
        console.log('Form Submitted, task_text: ', task_text);
        console.log('USER:!', userData);

        const { data, error } = await supabase
            .from('Tasks')
            .insert([
                { text: task_text, 
                  user_id: await userData.data.session.user.id,
                 },
            ])
        
        if (error) {
            console.log('Error adding task: ', error);
        } else {
            await dataUpdate();
            task_text = '';            

        }

    }

    async function handleEditClick(task) {
      console.log(task.id);
      taskTextPlaceholder = task.text
      showSaveID = task.id;
      currentlyEditing = true;
    }

    async function handleSaveClick(task) {
      console.log(task);

      if(task.text !== taskTextPlaceholder) {

      const { data, error } = await supabase
      .from('Tasks')
      .update({ text: task.text })
      .eq('id', task.id)

      if (error) {
            console.log('Error editing task: ', error);
        } else {
            await dataUpdate();
            currentlyEditing = false;
        }
        
      } 
    
    }

    async function handleDeleteClick(task) {
      console.log(task.id);


    const { data, error } = await supabase
      .from('Tasks')
      .delete()
      .eq('id', task.id)

      if (error) {
            console.log('Error deleting task: ', error);
        } else {
            await dataUpdate();

        }

    }
</script>

    
      
    <header>
        <h1>Task List</h1>
        <form id="new-task-form" autocomplete="off" on:submit|preventDefault={handleOnSubmit}>
          <input
            type="text"
            id="new-task-input"
            placeholder="What do you have planned?"
            bind:value={task_text}
          />
          <input type="submit" id="new-task-submit" value="Add Task" />
        </form>
      </header>
      <main>
        <section class="task-list">
          <h2>Tasks</h2>
          <div id="tasks">
            {#each tasks as task}
            <div class="task">
              <div class="content">
                {#if showSaveID !== task.id || currentlyEditing == false}
                {task.text}
                {/if}
                {#if showSaveID == task.id && currentlyEditing == true}
                <input type="text" id={task.id} class="task-input" bind:value={task.text}>
                {/if}
              </div>
              <div class="actions">
                {#if showSaveID !== task.id || currentlyEditing == false}
                <button class="edit" id={task.id} on:click={() => handleEditClick(task)}>Edit</button>
                {/if}
                {#if showSaveID == task.id && currentlyEditing == true}
                <button class="save" on:click={() => handleSaveClick(task)}>Save</button>
                {/if}
                <button class="delete" on:click={() => handleDeleteClick(task)}>Delete</button>
              </div>
            </div>
            {/each}
          </div>
        </section>
      </main>
