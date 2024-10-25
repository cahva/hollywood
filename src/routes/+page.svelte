<script lang="ts">
  type Person = {
    id: string | number;
    name: string;
    lastname: string;
    profession: string;
  };

  type Newperson = Omit<Person, "id">;

  let people = $state<Person[]>([
    // { id: 1, name: 'John', lastname: 'Woo', profession: 'Director' },
    // { id: 2, name: 'Clint', lastname: 'Eastwood', profession: 'Director, Actor' },
    // { id: 3, name: 'Meryl', lastname: 'Streep', profession: 'Actor' },
    // { id: 4, name: 'Steven', lastname: 'Spielberg', profession: 'Director' },
    // { id: 5, name: 'Tom', lastname: 'Hanks', profession: 'Actor' },
    // { id: 6, name: 'Martin', lastname: 'Scorsese', profession: 'Director' },
    // { id: 7, name: 'Quentin', lastname: 'Tarantino', profession: 'Director, Screenwriter' },
    // { id: 8, name: 'Robert', lastname: 'De Niro', profession: 'Actor' },
    // { id: 9, name: 'Christopher', lastname: 'Nolan', profession: 'Director, Writer' },
    // { id: 10, name: 'Leonardo', lastname: 'DiCaprio', profession: 'Actor' },
    // { id: 11, name: 'Emma', lastname: 'Stone', profession: 'Actor' },
    // { id: 12, name: 'Ridley', lastname: 'Scott', profession: 'Director' },
    // { id: 13, name: 'Cate', lastname: 'Blanchett', profession: 'Actor' },
    // { id: 14, name: 'James', lastname: 'Cameron', profession: 'Director' },
    // { id: 15, name: 'Natalie', lastname: 'Portman', profession: 'Actor' },
  ]);

  let newPerson = $state<Newperson>({ name: "", lastname: "", profession: "" });
  let snapshots: Person[][] = [];
  let history = $state(-1);

  function addPerson(e) {
    e.preventDefault();

    if (
      newPerson.name.trim() === "" ||
      newPerson.lastname.trim() === "" ||
      newPerson.profession.trim() === ""
    ) {
      alert("Empty fields");
      return false;
    }
    people.push({ ...newPerson, id: window.crypto.randomUUID() });
    takeSnapshot();

    resetPerson();
  }

  function removePerson(id: string | number) {
    people = people.filter((p) => p.id != id);
    takeSnapshot();
  }

  function resetPerson() {
    newPerson = { name: "", lastname: "", profession: "" };
  }

  function undo() {
    people = snapshots[--history];
  }

  function redo() {
    people = snapshots[++history];
  }

  function takeSnapshot() {
    history++;
    snapshots.push($state.snapshot(people));
  }

  function isValid(): boolean {
    return Object.values(newPerson).every((value) => value.trim() !== "");
  }

  $inspect(snapshots.length);
</script>

<h1>Film stuff</h1>
<div>
  <button disabled={history === -1} onclick={undo}>Undo</button>
  <button disabled={history === snapshots.length - 1} onclick={redo}
    >Redo</button
  >
  <button onclick={() => takeSnapshot()}>&quot;Save&quot;</button>
  <span>History {history}</span>
</div>
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Lastname</th>
      <th>Profession</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    {#each people as person}
      <tr>
        <td><input type="text" bind:value={person.name} /></td>
        <td><input type="text" bind:value={person.lastname} /></td>
        <td><input type="text" bind:value={person.profession} /></td>
        <td
          ><button type="button" onclick={() => removePerson(person.id)}
            >Remove</button
          ></td
        >
      </tr>
    {/each}
  </tbody>
</table>
<form onsubmit={addPerson}>
  <fieldset>
    <legend>Add new film related person :)</legend>
    <label
      ><span>Name</span><input type="text" bind:value={newPerson.name} /></label
    >
    <label
      ><span>LastName</span><input
        type="text"
        bind:value={newPerson.lastname}
      /></label
    >
    <label
      ><span>Profession</span><input
        type="text"
        bind:value={newPerson.profession}
      /></label
    >
    <button disabled={!isValid()}>Add</button>
  </fieldset>
</form>

<style>
  label {
    display: flex;
    gap: 0.5rem;
  }
</style>
