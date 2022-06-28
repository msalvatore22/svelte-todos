<script>
  import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';
  import { flip } from 'svelte/animate';

	const [send, receive] = crossfade({
		duration: d => Math.sqrt(d * 200),

		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: t => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
		}
	});


  let todos = [
    { id: 1, done: false, description: "create todo app"},
    { id: 2, done: false, description: "workout"},
    { id: 3, done: false, description: "make dinner"},
    { id: 4, done: true,  description: "walk the dog"}
  ]

  let uid = todos.length + 1;

  function add(input) {
    const todo = {
      id: uid++,
      done: false,
      description: input.value
    };

    todos = [...todos, todo];
    input.value = '';
  }

  function remove(todo){
    todos = todos.filter(td => td !== todo)
  }
  
  function mark(todo, done) {
		todo.done = done;
		remove(todo);
		todos = todos.concat(todo);
	}
</script>

<div class='box'>
  <input
		placeholder="What needs to be done?"
		on:keydown="{event => event.key === 'Enter' && add(event.target)}"
	>
  <div class='left'>
    <h2>todo</h2>
    {#each todos.filter(t => !t.done) as todo (todo.id)}
			<label
        in:receive="{{key: todo.id}}"
				out:send="{{key: todo.id}}"
        animate:flip
      >
				<input type=checkbox on:change={() => mark(todo, true)}>
				{todo.description}
				<button on:click="{() => remove(todo)}">X</button>
			</label>
		{/each}
  </div>
  <div class='right'>
		<h2>done</h2>
		{#each todos.filter(t => t.done) as todo (todo.id)}
			<label 
        class="done"
	      in:receive="{{key: todo.id}}"
	      out:send="{{key: todo.id}}"
        animate:flip="{{duration: 200}}"
      >
				<input type=checkbox checked on:change={() => mark(todo, false)}>
				{todo.description}
				<button on:click="{() => remove(todo)}">X</button>
			</label>
		{/each}
	</div>
 
</div>

<style>
  .box {
    display: grid;
    grid-template-columns: 1fr 1fr;
    grid-gap: 1rem;
    margin: 0 auto;
    max-width: 80%;
    font-family: 'Open Sans', sans-serif;
  }

  h2 {
    grid-row-start: 1;
    grid-row-end: 1;
  }

  .left {
    display: flex;
    flex-direction: column;
  }

  .right {
    display: flex;
    flex-direction: column;
  }

  .box > input {
		grid-column: 1/3;
    font-size: 30px;
	}

  input[type="checkbox"] {
		position: absolute;
		left: 0.5em;
		top: 0.6em;
		margin: 0;
    cursor: pointer;
	}

  label {
		position: relative;
		line-height: 1.2;
		padding: 0.5em 2.5em 0.5em 2em;
		margin: 0 0 0.5em 0;
		border-radius: 2px;
		user-select: none;
		border: 1px solid hsl(240, 8%, 70%);
		background-color:hsl(240, 8%, 93%);
		color: #333;
	}

  button {
		position: absolute;
		top: 10%;
		right: 0.2em;
		width: 2em;
		height: 80%;
		border-style: solid 1px black;
    background-color: none;
		cursor: pointer;
    color: black;
	}

  button:hover {
    color: red
  }

  .done {
		border: 1px solid hsl(240, 8%, 90%);
		background-color:hsl(240, 8%, 98%);
	}

</style>
