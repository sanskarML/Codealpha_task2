# Codealpha_task2
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Basic Calculator</title>
    <style>
        /* =========================================
           CSS Styling & Layout
           ========================================= */
        :root {
            --bg-color: #e0e5ec;
            --calc-bg: #22252d;
            --display-bg: #292d36;
            --btn-bg: #272b33;
            --btn-hover: #3a3f4b;
            --text-color: #ffffff;
            --operator-color: #26e698;
            --clear-color: #ff6b6b;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .calculator {
            background-color: var(--calc-bg);
            border-radius: 20px;
            box-shadow: 0 15px 30px rgba(0, 0, 0, 0.3);
            width: 320px;
            overflow: hidden;
            padding: 20px;
        }

        /* --- Display Screen --- */
        .display-container {
            background-color: var(--display-bg);
            border-radius: 10px;
            padding: 20px;
            margin-bottom: 20px;
            text-align: right;
            box-shadow: inset 0 2px 5px rgba(0,0,0,0.2);
        }

        .previous-operand {
            color: rgba(255, 255, 255, 0.6);
            font-size: 1rem;
            min-height: 24px;
            word-wrap: break-word;
        }

        .current-operand {
            color: var(--text-color);
            font-size: 2.5rem;
            font-weight: bold;
            word-wrap: break-word;
            word-break: break-all;
        }

        /* --- Buttons Grid --- */
        .buttons {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 12px;
        }

        button {
            border: none;
            outline: none;
            background-color: var(--btn-bg);
            color: var(--text-color);
            font-size: 1.5rem;
            border-radius: 10px;
            padding: 15px;
            cursor: pointer;
            transition: background-color 0.2s ease, transform 0.1s ease;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }

        button:hover {
            background-color: var(--btn-hover);
        }

        button:active {
            transform: scale(0.95);
        }

        /* Special Button Styling */
        .operator {
            color: var(--operator-color);
            font-weight: bold;
            background-color: #292d36;
        }

        .clear {
            color: var(--clear-color);
            font-weight: bold;
        }

        .span-two {
            grid-column: span 2;
        }
    </style>
</head>
<body>

    <div class="calculator">
        <div class="display-container">
            <div class="previous-operand" id="previous-operand"></div>
            <div class="current-operand" id="current-operand">0</div>
        </div>
        <div class="buttons">
            <button class="clear span-two" id="clear">AC</button>
            <button class="operator" data-action="delete">DEL</button>
            <button class="operator" data-action="divide">÷</button>

            <button class="number" data-number="7">7</button>
            <button class="number" data-number="8">8</button>
            <button class="number" data-number="9">9</button>
            <button class="operator" data-action="multiply">×</button>

            <button class="number" data-number="4">4</button>
            <button class="number" data-number="5">5</button>
            <button class="number" data-number="6">6</button>
            <button class="operator" data-action="subtract">−</button>

            <button class="number" data-number="1">1</button>
            <button class="number" data-number="2">2</button>
            <button class="number" data-number="3">3</button>
            <button class="operator" data-action="add">+</button>

            <button class="number span-two" data-number="0">0</button>
            <button class="number" data-number=".">.</button>
            <button class="operator" id="equals">=</button>
        </div>
    </div>

    <script>
        class Calculator {
            constructor(previousOperandTextElement, currentOperandTextElement) {
                this.previousOperandTextElement = previousOperandTextElement;
                this.currentOperandTextElement = currentOperandTextElement;
                this.clear();
            }

            clear() {
                this.currentOperand = '0';
                this.previousOperand = '';
                this.operation = undefined;
            }

            delete() {
                if (this.currentOperand === '0') return;
                this.currentOperand = this.currentOperand.toString().slice(0, -1);
                if (this.currentOperand === '') this.currentOperand = '0';
            }

            appendNumber(number) {
                if (number === '.' && this.currentOperand.includes('.')) return;
                if (this.currentOperand === '0' && number !== '.') {
                    this.currentOperand = number.toString();
                } else {
                    this.currentOperand = this.currentOperand.toString() + number.toString();
                }
            }

            chooseOperation(operation) {
                if (this.currentOperand === '0' && this.previousOperand === '') return;
                if (this.previousOperand !== '') {
                    this.compute();
                }
                this.operation = operation;
                this.previousOperand = this.currentOperand;
                this.currentOperand = '0';
            }

            compute() {
                let computation;
                const prev = parseFloat(this.previousOperand);
                const current = parseFloat(this.currentOperand);
                if (isNaN(prev) || isNaN(current)) return;

                switch (this.operation) {
                    case '+':
                        computation = prev + current;
                        break;
                    case '−':
                        computation = prev - current;
                        break;
                    case '×':
                        computation = prev * current;
                        break;
                    case '÷':
                        if (current === 0) {
                            alert("Cannot divide by zero!");
                            this.clear();
                            return;
                        }
                        computation = prev / current;
                        break;
                    default:
                        return;
                }
                
                // Fix floating point precision issues (e.g., 0.1 + 0.2)
                this.currentOperand = Math.round(computation * 100000000) / 100000000;
                this.operation = undefined;
                this.previousOperand = '';
            }

            updateDisplay() {
                this.currentOperandTextElement.innerText = this.currentOperand;
                if (this.operation != null) {
                    this.previousOperandTextElement.innerText = 
                        `${this.previousOperand} ${this.operation}`;
                } else {
                    this.previousOperandTextElement.innerText = '';
                }
            }
        }

        // DOM Elements
        const numberButtons = document.querySelectorAll('[data-number]');
        const operationButtons = document.querySelectorAll('[data-action="add"], [data-action="subtract"], [data-action="multiply"], [data-action="divide"]');
        const equalsButton = document.getElementById('equals');
        const deleteButton = document.querySelector('[data-action="delete"]');
        const clearButton = document.getElementById('clear');
        const previousOperandTextElement = document.getElementById('previous-operand');
        const currentOperandTextElement = document.getElementById('current-operand');

        const calculator = new Calculator(previousOperandTextElement, currentOperandTextElement);

        // Event Listeners for Clicks
        numberButtons.forEach(button => {
            button.addEventListener('click', () => {
                calculator.appendNumber(button.innerText);
                calculator.updateDisplay();
            });
        });

        operationButtons.forEach(button => {
            button.addEventListener('click', () => {
                calculator.chooseOperation(button.innerText);
                calculator.updateDisplay();
            });
        });

        equalsButton.addEventListener('click', () => {
            calculator.compute();
            calculator.updateDisplay();
        });

        clearButton.addEventListener('click', () => {
            calculator.clear();
            calculator.updateDisplay();
        });

        deleteButton.addEventListener('click', () => {
            calculator.delete();
            calculator.updateDisplay();
        });

        // Bonus: Keyboard Support
        document.addEventListener('keydown', (e) => {
            if ((e.key >= 0 && e.key <= 9) || e.key === '.') {
                calculator.appendNumber(e.key);
                calculator.updateDisplay();
            }
            if (e.key === '=' || e.key === 'Enter') {
                e.preventDefault();
                calculator.compute();
                calculator.updateDisplay();
            }
            if (e.key === 'Backspace') {
                calculator.delete();
                calculator.updateDisplay();
            }
            if (e.key === 'Escape') {
                calculator.clear();
                calculator.updateDisplay();
            }
            if (e.key === '+' || e.key === '-') {
                // Map keyboard minus to the visual minus used in logic
                const op = e.key === '-' ? '−' : e.key;
                calculator.chooseOperation(op);
                calculator.updateDisplay();
            }
            if (e.key === '*' || e.key === '/') {
                const op = e.key === '*' ? '×' : '÷';
                calculator.chooseOperation(op);
                calculator.updateDisplay();
            }
        });
    </script>
</body>
</html>
