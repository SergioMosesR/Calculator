<script>
    import { evaluate } from "mathjs";
    import { onMount } from "svelte";

    let display = ""; // input angka
    let history = [];

    // load history dari local storage
    onMount(() => {
        const savedHistory = localStorage.getItem("calcHistory");
        if (savedHistory) {
            history = JSON.parse(savedHistory);
        }
    });

    // menambah operator ke display
    function append(value) {
        display += value;
    }

    // clear display
    function clearDisplay() {
        display = "";
    }

    // kalkulasi dan simpan di local storage
    function calculate() {
        try {
            let result = evaluate(display);
            history = [...history, `${display} = ${result}`].slice(-100);
            localStorage.setItem("calcHistory", JSON.stringify(history));
            display = result;
        } catch (e) {
            display = "Error";
        }
    }

    // fungsi akar
    function sqrt() {
        try {
            display = evaluate(`sqrt(${display})`);
        } catch (e) {
            display = "Error";
        }
    }

    // fungsi persen %
    function percent() {
        try {
            display = evaluate(`${display} / 100`);
        } catch (e) {
            display = "Error";
        }
    }

    // fungsi backspace
    function backSpace() {
        display = display.slice(0, -1);
    }

    // fungsi keydown untuk keyboard
    function handleKeydown(event) {
        if (event.key === "Enter") {
            calculate();
        } else if (event.key === "Backspace") {
            backSpace();
        }
    }

    // fungsi clear history
    function clearHistory() {
        history = [];
        localStorage.removeItem("calcHistory");
    }
</script>

<title>Calculator with Svelte</title>

<div class="container">
    <div class="calculator">
        <input
            class="display"
            bind:value={display}
            on:keydown={handleKeydown}
            placeholder="0"
            autofocus
        />

        <div class="buttons">
            <button on:click={percent}>%</button>
            <button on:click={sqrt}>√</button>
            <button on:click={() => append("/")}>÷</button>
            <button on:click={() => append("^")}>xʸ</button>

            <button on:click={() => append("7")}>7</button>
            <button on:click={() => append("8")}>8</button>
            <button on:click={() => append("9")}>9</button>
            <button on:click={() => append("*")}>×</button>

            <button on:click={() => append("4")}>4</button>
            <button on:click={() => append("5")}>5</button>
            <button on:click={() => append("6")}>6</button>
            <button on:click={() => append("-")}>−</button>

            <button on:click={() => append("1")}>1</button>
            <button on:click={() => append("2")}>2</button>
            <button on:click={() => append("3")}>3</button>
            <button on:click={() => append("+")}>+</button>

            <button on:click={() => append("0")} class="zero">0</button>
            <button on:click={() => append(".")}>.</button>
            <button on:click={backSpace} class="backspace">←</button>
            <button on:click={clearDisplay} class="clear">C</button>

            <button on:click={calculate} class="equal">=</button>
        </div>
    </div>

    {#if history.length > 0}
        <div class="history-card">
            <h3>History</h3>
            <ul>
                {#each history as item}
                    <li>{item}</li>
                {/each}
            </ul>
            <button class="clearHistory" on:click={clearHistory}>Clear History</button>
        </div>
    {/if}
</div>

<style>
    .container {
        display: flex;
        align-items: flex-start;
        justify-content: center;
        gap: 20px;
        margin-top: 20px;
    }

    .calculator {
        width: 300px;
        padding: 20px;
        border-radius: 10px;
        background: #222;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
    }

    .display {
        width: 89%;
        padding: 15px;
        background: #333;
        color: white;
        font-size: 2em;
        text-align: right;
        border-radius: 5px;
        margin-bottom: 10px;
        border: none;
        outline: none;
    }

    .buttons {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        gap: 5px;
    }

    button {
        padding: 15px;
        font-size: 1.5em;
        border: none;
        border-radius: 5px;
        background: #444;
        color: white;
        cursor: pointer;
        transition: 0.2s;
    }

    button:hover {
        background: #666;
    }

    .clear {
        background: red;
    }

    .equal {
        background: green;
        grid-column: span 4;
    }

    .history-card {
        width: 250px;
        padding: 15px;
        background: #333;
        border-radius: 10px;
        color: white;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
    }

    .history-card h3 {
        margin: 0 0 10px;
        font-size: 1.2em;
        text-align: center;
    }

    .history-card ul {
        list-style: none;
        padding: 0;
        max-height: 200px;
        overflow-y: auto;
    }

    .history-card li {
        padding: 5px;
        font-size: 1em;
        border-bottom: 1px solid #555;
    }

    .history-card li:last-child {
        border-bottom: none;
    }

    .clearHistory {
        width: 100%;
        margin-top: 10px;
        font-size: 1em;
        background: red;
        border: none;
        border-radius: 5px;
        padding: 10px;
        cursor: pointer;
    }

    @media (max-width: 600px) {
        .container {
            flex-direction: column;
            align-items: center;
        }

        .history-card {
            width: 100%;
        }
    }
</style>
