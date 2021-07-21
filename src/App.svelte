<script>
	const numbers = [7, 8, 9, 4, 5, 6, 1, 2, 3, 0];
	const symbols = ["-", "+", "*", "/", "=", "AC", "DEL", "."];

	const STATUS = {
		VALID: "valid",
		INVALID: "invalid",
		INVALID_SYMBOL: "invalid_symbol",
		MERGE_NUMBER: "merge_number",
	};

	let problem = [];
	let resultColor = "rgba(0, 0, 0, .75)";

	const performOperation = (val1, op, val2) => {
		console.log(val1, op, val2);
		switch (op) {
			case "+":
				return val1 + val2;
			case "-":
				return val1 - val2;
			case "*":
				return val1 * val2;
			case "/":
				return val1 / val2;
		}
	};

	const solveProblem = () => {
		const stack = [];
		for (let i = 0; i < problem.length; i++) {
			let key = problem[i];
			const isCurrentSymbol = symbols.includes(key);
			if (!isCurrentSymbol || key == "+" || key == "-") {
				stack.push(key);
			} else if (key == "*" || key == "/") {
				const val1 = stack.pop();
				const val2 = problem[++i];
				const result = performOperation(val1, key, val2);
				stack.push(result);
			}
		}
		while (stack.length != 1) {
			const val1 = stack.shift();
			const op = stack.shift();
			const val2 = stack.shift();
			const result = performOperation(val1, op, val2);
			stack.unshift(result);
		}
		problem = stack;
	};

	// check key status
	const checkKeyStatus = (key) => {
		const isCurrentSymbol = symbols.includes(key);
		if (problem.length != 0) {
			const lastKey = problem[problem.length - 1];
			const isPreviousSymbol = symbols.includes(lastKey);
			// two consecutive symbol are not valid
			if (isPreviousSymbol && isCurrentSymbol) {
				return STATUS.INVALID_SYMBOL;
			}
			// check for next number
			else if (!isPreviousSymbol && !isCurrentSymbol) {
				return STATUS.MERGE_NUMBER;
			}
			// more validations can be added, for brackets :)
		}
		// first key as symbol is not valid
		else if (isCurrentSymbol) {
			return STATUS.INVALID_SYMBOL;
		}
		return STATUS.VALID;
	};

	// add keys to problem, can be number of operat
	const addToProblem = (key) => {
		console.log(problem);
		resultColor = "rgb(0, 0, 0, .75)";
		if (key == "AC") {
			return (problem = []);
		} else if (key == "DEL") {
			problem.pop();
			return (problem = [...problem]);
		}
		const status = checkKeyStatus(key);
		if (status == STATUS.VALID) {
			if (key == "=") {
				solveProblem();
			} else {
				problem.push(key);
				problem = [...problem];
			}
		} else if (status == STATUS.MERGE_NUMBER) {
			let val = problem.pop() * 10 + key;
			problem.push(val);
			problem = [...problem];
		} else {
			resultColor = "white";
		}
	};
</script>

<main>
	<div class="calculator-grid">
		<div class="result" style="background: {resultColor}">
			{problem.join("")}
		</div>
		<div class="numpad" style="grid-template-columns: repeat(2, 139px);">
			<div class="numitem" style="width: 100px;" on:click={() => addToProblem("AC")}>AC</div>
			<div class="numpad" style="grid-template-columns: repeat(2, 69px);">
				<div class="numitem" on:click={() => addToProblem("DEL")}>DEL</div>
				<div class="numitem" on:click={() => addToProblem("/")}>/</div>
			</div>
		</div>
		
		<div class="numpad">
			<div class="numitem" on:click={() => addToProblem("7")}>7</div>
			<div class="numitem" on:click={() => addToProblem("8")}>8</div>
			<div class="numitem" on:click={() => addToProblem("9")}>9</div>
			<div class="numitem" on:click={() => addToProblem("*")}>*</div>
		</div>
		<div class="numpad">
			<div class="numitem" on:click={() => addToProblem("4")}>4</div>
			<div class="numitem" on:click={() => addToProblem("5")}>5</div>
			<div class="numitem" on:click={() => addToProblem("6")}>6</div>
			<div class="numitem" on:click={() => addToProblem("+")}>+</div>
		</div>
		<div class="numpad">
			<div class="numitem" on:click={() => addToProblem("1")}>1</div>
			<div class="numitem" on:click={() => addToProblem("2")}>2</div>
			<div class="numitem" on:click={() => addToProblem("3")}>3</div>
			<div class="numitem" on:click={() => addToProblem("-")}>-</div>
		</div>
		<div class="numpad">
			<div class="numitem" on:click={() => addToProblem(".")}>.</div>
			<div class="numitem" on:click={() => addToProblem("0")}>0</div>
			<div class="numitem" style="width: 100px;" on:click={() => addToProblem("=")}>=</div>
		</div>
	</div>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		margin: 0 auto;
		background: linear-gradient(to right, #00AAFF, #00FF6C);
		width: 100%;
		height: 100vh;
	}
	.result {
		grid-column: 1 / -1;
		min-height: 50px;
		background: rgba(0, 0, 0, .75);
		text-align: left;
		margin: 1px;
		display: flex;
		align-items: flex-end;
		justify-content: space-around;
		flex-direction: column;
		padding: 10px;
		word-wrap: break-word;
		word-break: break-all;
		color: white;
		font-size: 1.5rem;
		width: 260px;
		font-weight: normal;
		overflow-wrap: normal;
	}
	.numpad {
		display: grid;
		grid-template-columns: repeat(4, 69px);
		grid-column-gap: 1px;
	}
	.numitem {
		width: 30px;
		padding: 18px;
		margin: 1px;
		/* display: inline-grid; */
		background: rgba(255, 255, 255, .75);;
		text-align: center;
		cursor: pointer;
		border: 1px solid white;
  		outline: none;
		display: -webkit-inline-box;
		font-weight: bolder;
		font-size: 1.4rem;
		align-content: center;
		justify-content: center;
		display: block;
	}
	.numitem:hover{
		background-color: rgba(255, 255, 255, .9);
	}
	.calculator-grid {
		width: 300px;
		margin: auto;
		justify-content: center;
  		align-content: center

	}

</style>
