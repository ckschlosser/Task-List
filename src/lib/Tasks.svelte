<script lang="ts">
    import { onMount } from 'svelte';
    import { supabase } from "../supabaseClient";

    let taskTextPlaceholder;
    let showSaveID;
    let task_text;
    let currentlyEditing = false;
    $:tasks = [];


    // Get tasks from supabase
    onMount(async () => {
		
        let data = await supabase
            .from('Tasks')
            .select('*')

        tasks = data.data
        console.log('Tasks:', tasks);

	});

    async function handleOnSubmit() {
        console.log('Form Submitted, task_text: ', task_text);

        const { data, error } = await supabase
            .from('Tasks')
            .insert([
                { text: task_text },
            ])
        
        if (error) {
            console.log('Error adding task: ', error);
        } else {
            let afterAddData = await supabase
              .from('Tasks')
              .select('*')

            tasks = afterAddData.data;
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
            let afterEditData = await supabase
              .from('Tasks')
              .select('*')

            tasks = afterEditData.data;
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
            let afterDeleteData = await supabase
              .from('Tasks')
              .select('*')

            tasks = afterDeleteData.data


        }

    }

   /* window.addEventListener("load", () => {
  const form = document.querySelector("#new-task-form");
  const input = document.querySelector("#new-task-input");
  const list_el = document.querySelector("#tasks");

  form.addEventListener("submit", (e) => {
    
    console.log('I Added one');
    e.preventDefault();

    const task = input.value;
    if (!task) {
      alert("Please fill out the task");
      return;
    }

    const task_el = document.createElement("div");
    task_el.classList.add("task");

    const task_content_el = document.createElement("div");
    task_content_el.classList.add("content");

    task_el.appendChild(task_content_el);

    // Input
    const task_input_el = document.createElement("input");

    // Add
    task_input_el.classList.add("text");
    task_input_el.type = "text";
    task_input_el.value = task;
    task_input_el.setAttribute("readonly", "readonly");
    task_content_el.appendChild(task_input_el);
    const task_actions_el = document.createElement("div");

    task_actions_el.classList.add("actions");

    // Edit
    const task_edit_el = document.createElement("button");
    task_edit_el.classList.add("edit");
    task_edit_el.innerHTML = "Edit";

    // Delete
    const task_delete_el = document.createElement("button");
    task_delete_el.classList.add("delete");
    task_delete_el.innerHTML = "Delete";

    task_actions_el.appendChild(task_edit_el);
    task_actions_el.appendChild(task_delete_el);

    task_el.appendChild(task_actions_el);

    // Clicked Add
    list_el.appendChild(task_el);

    input.value = "";

    task_edit_el.addEventListener("click", () => {
      if (task_edit_el.innerText.toLowerCase() == "edit") {
        console.log('I Edited one');
        task_input_el.removeAttribute("readonly");
        task_input_el.focus();
        task_edit_el.innerText = "Save";
      } else {
        console.log('I Saved one');
        task_input_el.setAttribute("readonly", "readonly");
        task_edit_el.innerText = "Edit";
      }
    });

    task_delete_el.addEventListener("click", () => {
      list_el.removeChild(task_el);
    });
  });
});*/
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
