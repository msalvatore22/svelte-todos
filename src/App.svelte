<script>
  let todos = [
    { id: 1, done: false, description: "create todo app"},
    { id: 2, done: false, description: "workout"},
    { id: 3, done: false, description: "make dinner"}
  ]

  let uid = todos.length + 1;

  function add(input) {
    const todo = {
      id: uid++,
      done: false,
      description: input.value
    };

    todos = [todo, ...todos];
    input.value = '';
  }

  function remove(todo){
    todos = todos.filter(td => td !== todo)
  }
  console.log(todos)
</script>

<div>
  <input
		placeholder="what needs to be done?"
		on:keydown="{event => event.key === 'Enter' && add(event.target)}"
	>
  <ul>
    {#each todos as todo}
      <li>
        {todo.description} 
        <button on:click="{e => remove(todo)}">Delete</button>
      </li>
    {/each}
  </ul>
</div>

<style>

</style>
