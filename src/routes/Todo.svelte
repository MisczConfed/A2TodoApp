<script>
    import { refreshAll } from "$app/navigation";
    import {onMount} from "svelte";
    

    let todoItem = $state('');
    let todoList = $state([]);

    const colorTags = [
        { name: 'None', value: 'none', color: '#efe9e3' },
        { name: 'Red', value: 'red', color: '#ff6b6b' },
        { name: 'Yellow', value: 'yellow', color: '#ffd93d' },
        { name: 'Green', value: 'green', color: '#6bcf7f' },
        { name: 'Blue', value: 'blue', color: '#4d96ff' },
        { name: 'Purple', value: 'purple', color: '#c77dff' }
    ];

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
            done: false,
            color: 'none'
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
                <li data-color={item.color}>
                    <div class="item-content">
                        <div class="item-header">
                            <input type="checkbox" bind:checked={item.done} onchange={updateList}>&nbsp; &#128394;
                            <select bind:value={item.color} onchange={updateList} class="color-tag">
                                {#each colorTags as tag}
                                    <option value={tag.value}>{tag.name}</option>
                                {/each}
                            </select>
                            <span contenteditable="true" class:done={item.done}>{item.text}</span>
                        </div>
                    </div>
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
        gap: 6px;
        flex-wrap: wrap;
        padding: 8px 6px;
    }


    form input[type="text"] {
        flex: 1 1 320px;
        min-width: 140px;
        max-width: 60vw;
        border-radius: 10px;
        padding: 8px;
        background-color: rgb(239, 233, 227);
        border: #d9cfc7 solid 4px;
        font-family: "Sour Gummy", sans-serif;
        font-size: 18px;
        margin: 2px;
        box-sizing: border-box;
    }

    form input:focus[type="text"] {
        border: rgb(217, 207, 199) solid 4px;
        outline: none;
        background-color: rgb(239, 233, 227);
        color: rgb(124, 105, 82);
        margin: 2px;
    }

    form input:hover {
        border-radius: 10px;
        padding: 8px;
        background-color: #d9cfc7;
        border: solid 4px #efe9e3;
        margin: 2px;
    }


    .todo-list {
        width: 95%;
        max-width: 680px;
        margin: 12px auto;
        padding: 0 8px;
        box-sizing: border-box;
    }

    ul {
        list-style: none;
        padding: 0;
        margin: 0;
    }


    li input[type="checkbox"],
    .allBtn input[type="checkbox"] {
        height: 20px;
        width: 20px;
        accent-color: plum;
    }


    button[type="button"] {
        color: rgb(124, 105, 82);
        float: right;
        border-radius: 10px;
        border-color: white;
        padding: 6px 8px;
        background: transparent;
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

    .color-tag {
        margin: 0 4px;
        padding: 6px 8px;
        border-radius: 6px;
        border: 2px solid #d9cfc7;
        background-color: rgb(239, 233, 227);
        color: rgb(124, 105, 82);
        font-family: "Sour Gummy", sans-serif;
        cursor: pointer;
        font-size: 14px;
    }

  
    li {
        display: flex;
        align-items: center;
        justify-content: space-between;
        background-color: rgb(239, 233, 227);
        border: rgb(217, 207, 199) solid 4px;
        border-left: 8px solid transparent;
        padding: 10px;
        border-radius: 10px;
        margin-bottom: 8px;
        color: rgb(124, 105, 82);
        font-family: "Sour Gummy", sans-serif;
        gap: 8px;
        box-sizing: border-box;
    }


    .item-content {
        display: flex;
        align-items: center;
        gap: 8px;
        flex: 1 1 auto;
        min-width: 0; 
    }

   
    .item-header {
        display: flex;
        align-items: center;
        gap: 8px;
        flex: 1 1 auto;
        min-width: 0;
    }


    .item-header span,
    li span {
        flex: 1 1 auto;
        min-width: 0;
        overflow-wrap: anywhere;
        outline: none;
        font-size: 16px;
        line-height: 1.2;
    }

    li span.done {
        text-decoration: line-through;
        color: grey;
    }

  
    li[data-color="red"] { border-left-color: #ff6b6b; }
    li[data-color="yellow"] { border-left-color: #ffd93d; }
    li[data-color="green"] { border-left-color: #6bcf7f; }
    li[data-color="blue"] { border-left-color: #4d96ff; }
    li[data-color="purple"] { border-left-color: #c77dff; }

    li:hover {
        background-color: rgb(217, 207, 199);
        border: solid 4px rgb(239, 233, 227);
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
    }


    @media (max-width: 600px) {
        form input[type="text"] {
            flex-basis: 100%;
            max-width: 100%;
            font-size: 16px;
        }

        button[type="submit"] {
            height: 40px;
            width: 40px;
        }

        .color-tag {
            padding: 6px;
            font-size: 13px;
        }

        li {
            padding: 10px 8px;
            gap: 6px;
        }

        .item-header span {
            font-size: 15px;
        }
    }


    @media (max-width: 380px) {
        button[type="submit"] { height: 38px; width: 38px; }
        .interface button#delete[type="button"] { height: 40px; width: 40px; }
        .color-tag { font-size: 12px; padding: 5px; }
        li { padding: 8px; }
        li span { font-size: 14px; }
    }
</style>