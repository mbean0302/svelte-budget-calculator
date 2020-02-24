<script>
  import {setContext, onMount, afterUpdate} from "svelte";
  // Components
  import Navbar from "./Navbar.svelte";
  import Expense from "./Expense.svelte";
  import ExpensesList from "./ExpensesList.svelte";
  import Totals from "./Totals.svelte";
  import ExpenseForm from "./ExpenseForm.svelte";
  import Modal from "./Modal.svelte";
  // Data

  // Variables
  let expenses = [];
  // Set Editing Variables
  let setName = "";
  let setAmount = null;
  let setId = null;
  // Toggle Form variables
  let isFormOpen = false;
  // Reactive
  $: isEditing = setId ? true : false;
  $: total = expenses.reduce((acc, curr)=>{return (acc += curr.amount)}, 0);
  // Functions
  function showForm() {
    isFormOpen = true;
  }
  function closeForm() {
    isFormOpen = false;
    setName = "";
    setAmount = null;
    setId = null;
  }
  function  removeExpense(id) {
    expenses = expenses.filter(item => item.id !== id);
  }
  function clearExpenses() {
    expenses = [];
  }
  function addExpense({name, amount}) {
    let expense = {id:Math.random() * Date.now(), name, amount};
    expenses = [expense, ...expenses];
  }
  function setModifiedExpense(id) {
    let expense = expenses.find(item => item.id === id);
    setId = expense.id;
    setName = expense.name;
    setAmount = expense.amount;
    showForm();
  }
  function editExpense({name, amount}) {
    expenses = expenses.map(item => {
      return item.id === setId ? {...item, name:name, amount:amount} : {...item};
    });
    setId = null;
    setAmount = null;
    setName = "";
  }
  // Context
  setContext("remove", removeExpense);
  setContext("modify", setModifiedExpense);
  // Local Storage
  function setLocalStorage() {
    localStorage.setItem("expenses", JSON.stringify(expenses))
  }
  onMount(() => {
    expenses = localStorage.getItem("expenses") ? JSON.parse(localStorage.getItem("expenses")) : [];
  });
  afterUpdate(() => {
    setLocalStorage();
  })
</script>

<!-- <style></style> -->
<!-- CSS/STYLING -->
<!-- Styling is scoped locally -->


<!-- "props" shorthand for properties
makes components dynamic
set from outside -->

<!-- App HTML -->
<Navbar {showForm} />

<main class="content">
  {#if isFormOpen}
  <Modal>
    <ExpenseForm {addExpense} name="{setName}" amount="{setAmount}" {isEditing} {editExpense} {closeForm}/>
  </Modal>
  {/if}
  <Totals title="total expenses" {total} />
<ExpensesList {expenses} />
  <button type="button" class="btn btn-primary btn-block" on:click={clearExpenses}>
    clear expenses
  </button>
</main>
