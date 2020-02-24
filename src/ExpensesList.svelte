<script>
  import {fly} from "svelte/transition";
  import {flip} from "svelte/animate";
  import SectionTitle from "./Title.svelte";
  import Expense from "./Expense.svelte";
  export let expenses = [];
</script>

<style>
h2 {
  text-transform: capitalize;
}
</style>

<section>
  <SectionTitle title="expense list"/>
<ul>
  {#each expenses as expense, index (expense.id)}
    <div in:fly={{x:200, delay:index * 300}} out:fly={{x:-200, delay:index * 300}} animate:flip>
      <Expense {...expense} />
    </div>
    {:else}
    <h2>no expenses added to the list</h2>
  {/each}
</ul>
</section>