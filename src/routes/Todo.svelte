<script>
    import { refreshAll } from "$app/navigation";
    import {onMount} from "svelte";
    

    let todoItem = $state('');
    let todoList = $state([]);

    onMount(() => {
        // Load saved todoList from localStorage if available
        const savedList = localStorage.getItem('savedList');
        if (savedList) {
            todoList = (JSON.parse(savedList));
        }
    });

    function updateList() {
        return localStorage.setItem("savedList", JSON.stringify(todoList));
    }

    // add todoItem to todoList
    function addItem(event) {
        event.preventDefault();
        if (todoItem.trim().length === 0) {
            return;
        }
        todoList = [...todoList, {
            text: todoItem,
            done: false
        }];
        updateList();
        todoItem = '';
    }
    // remove todoItem from todoList
    function removeItem(index) {
        todoList.splice(index,1);
        updateList();
    }

     // remove all items that have the done class / done === true
    function deleteDone() {
        todoList = todoList.filter(item => !item.done);
        updateList();
    }
    function clearAll() {
        location.reload();
        localStorage.clear();
    }

</script>
    <div class="interface">
        <form onsubmit={addItem}>

            <!-- Button to delete all selected/done items -->
            <button type="button" id="delete" onclick={deleteDone}>&#128465;</button>

            <!-- Checkbox to mark all items as done/undone -->
            <div class="allBtn">
                <input
                    type="checkbox" 
                    onchange={(e) => {
                        const checked = e.currentTarget.checked;
                        todoList = todoList.map(item => ({ ...item, done: checked }));
                        updateList();
                    }}
                    checked={todoList.length > 0 && todoList.every(item => item.done)}/>
            </div>

                <!-- Input field to add new todo item -->
            <input type="text" placeholder="Add a task..." bind:value={todoItem} />
            <button type="submit" onclick={updateList}>&#128934</button>

            <!-- Delete all items regardless of their done status -->
            <button type="submit" style="font-size: 14px; font-weight: bold" onclick={() => clearAll()}>&#10227;</button>
        </form>
    </div>

    <div class="todo-list">
        <ul>
            {#each todoList as item, index}
                <li>
                    <input type="checkbox" bind:checked={item.done} onchange={updateList}>&nbsp; &#128394;
                    <span contenteditable="true" class:done={item.done}>{item.text}</span>
                    <button type="button" onclick={() => removeItem(index)}>&times;</button>
                </li>
            {/each}
        </ul>
    </div>








<style>
    form {
        text-align: center;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    form input {
        width: 35vw;
        border-radius: 10px;
        padding: 8px;
        background-color: rgb(239, 233, 227);
        border: rgb(217, 207, 199) solid 4px;
        font-family: "Sour Gummy", sans-serif;
        font-size: 18px;
        margin: 2px;
        
    }

    form input:focus[type="text"] {
        border: rgb(217, 207, 199) solid 4px;
        outline: none;
        background-color: rgb(239, 233, 227);
        color: rgb(124, 105, 82);
        margin: 2px;
    }

    form input:hover {
        width: 35vw;
        border-radius: 10px;
        padding: 8px;
        background-color: rgb(217, 207, 199);
        border: solid 4px rgb(239, 233, 227);
        margin: 2px;
    }
    
    .todo-list {
        width: 41vw;
        margin: auto;
    }

    ul {
        list-style: none;
        padding: 0;
    }

    li input[type="checkbox"] {
        height: 15px;
        width: 15px;
        accent-color: plum;
        padding-left: 5px;  
    }

    div input[type="checkbox"] {
        height: 15px;
        width: 15px;
        accent-color: plum;
        margin: 0; 
    }

    button[type="button"] {
        color: rgb(124, 105, 82);
        float: right;
        border-radius: 10px;
        border-color: white;
    }
    button[type="submit"] {
        height: 45px;
        width: 45px;
        border-radius: 10px;
        background-color: rgb(239, 233, 227);
        border: rgb(217, 207, 199) solid 4px;
        color: rgb(124, 105, 82);
        margin: 2px;
        outline: none;
        
    }

    button:hover[type="submit"] {
        background-color: rgb(217, 207, 199);
        border: solid 4px rgb(239, 233, 227);
    }

    button:hover[type="button"] {
        background-color: rgb(217, 207, 199);
    }

    li {
        background-color: rgb(239, 233, 227);
        border: rgb(217, 207, 199) solid 4px;
        padding: 10px;
        border-radius: 10px;
        margin-bottom: 5px;
        color: rgb(124, 105, 82);
        font-family: "Sour Gummy", sans-serif;
        
    }

    li:hover {
        background-color: rgb(217, 207, 199);
        border: solid 4px rgb(239, 233, 227);
    }
    
    li span.done {
        text-decoration: line-through;
        color: grey;
    
    }

    .interface button#delete[type="button"] {
        height: 45px;
        width: 45px;
        border-radius: 10px;
        background-color: rgb(239, 233, 227);
        border: rgb(217, 207, 199) solid 4px;
        color: rgb(124, 105, 82);
        display: inline-block;
        margin: 2px;
        font-size: x-large;
        outline: none;
    }

    .interface button#delete:hover[type="button"] {
        background-color: rgb(217, 207, 199);
        border: solid 4px rgb(239, 233, 227);
    }
    

    .allBtn {
        height: 38px;
        width: 38px;
        border-radius: 10px;
        background-color: rgb(239, 233, 227);
        border: rgb(217, 207, 199) solid 4px;
        color: rgb(124, 105, 82);
        display: inline-block;
        margin: 2px;
        font-size: large;
        align-content: center;
        
        input[type="checkbox"] {
            margin-top: 5px;
        }

        input:focus {
            outline: none;
        }   
    }

    .allBtn:hover {
        background-color: rgb(217, 207, 199);
        border: solid 4px rgb(239, 233, 227);    
    }

    .allBtn:focus {
        outline: none;
    }


</style>