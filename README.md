<!DOCTYPE html><html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Umbra Genesis</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@picocss/pico@1/css/pico.min.css">
    <style>
        body {
            background: url('/mnt/data/A_digital_illustration_presents_a_futuristic,_neon.png') no-repeat center center fixed;
            background-size: cover;
            color: #00ffea;
            font-family: 'Orbitron', sans-serif;
        }
        main.container {
            padding-top: 3rem;
        }
        .task-card {
            background: rgba(0,0,0,0.7);
            border-radius: 12px;
            padding: 1rem;
            margin-bottom: 1rem;
            transition: transform 0.2s, box-shadow 0.2s;
        }
        .task-card:hover {
            transform: translateY(-3px);
            box-shadow: 0 0 15px #00ffea;
        }
        button, input, select {
            background: rgba(0,0,0,0.5);
            color: #00ffea;
            border: 1px solid #00ffea;
        }
        .level-display {
            font-size: 1.2rem;
            margin-bottom: 1rem;
        }
        h1, h2, h3 {
            color: #00ffea;
        }
    </style>
</head>
<body>
    <nav class="container-fluid">
        <ul><li><strong>Umbra Genesis</strong></li></ul>
        <ul>
            <li><a href="#dashboard">Dashboard</a></li>
            <li><a href="#tasks">Tasks</a></li>
            <li><a href="#add-task" role="button">Add Task</a></li>
        </ul>
    </nav><main class="container" id="dashboard">
    <div class="grid">
        <section>
            <hgroup>
                <h2>Adithya Boini</h2>
                <h3>Your System. Your Orders.</h3>
            </hgroup>
            <div class="level-display">Level: <span id="level">1</span> | XP: <span id="xp">0</span></div>
            <button onclick="generateTask()">Generate Random Task</button>
        </section>

        <section id="tasks">
            <h3>Task List</h3>
            <div id="task-list"></div>
        </section>

        <section id="add-task">
            <h3>Add Custom Task</h3>
            <form id="task-form" class="grid">
                <input type="text" id="task-name" placeholder="Task Name" required />
                <input type="datetime-local" id="task-deadline" required />
                <select id="task-difficulty" required>
                    <option value="Easy">Easy</option>
                    <option value="Medium">Medium</option>
                    <option value="Hard">Hard</option>
                </select>
                <input type="text" id="task-category" placeholder="Category" />
                <button type="submit">Add Task</button>
            </form>
        </section>
    </div>
</main>

<script>
    const predefinedTasks = [
        'Study Physics for 1 hour',
        'Workout - 50 pushups',
        'Read a chapter of a book',
        'Meditate for 20 minutes',
        'Practice coding for 1 hour'
    ];

    let tasks = JSON.parse(localStorage.getItem('tasks')) || [];
    let xp = parseInt(localStorage.getItem('xp')) || 0;
    let level = Math.floor(xp / 100) + 1;

    document.getElementById('xp').innerText = xp;
    document.getElementById('level').innerText = level;

    function saveTasks() {
        localStorage.setItem('tasks', JSON.stringify(tasks));
    }

    function renderTasks() {
        const taskList = document.getElementById('task-list');
        taskList.innerHTML = '';
        tasks.forEach((task, index) => {
            const card = document.createElement('div');
            card.className = 'task-card';
            card.innerHTML = `
                <input type='checkbox' ${task.completed ? 'checked' : ''} onchange='toggleTask(${index})' />
                <strong>${task.name}</strong> <em>(${task.category || 'General'})</em><br>
                Deadline: ${task.deadline} | Difficulty: ${task.difficulty}<br>
                <span>Time spent: <span id='timer-${index}'>0</span> min</span> <button onclick='startTimer(${index})'>Start</button>
            `;
            taskList.appendChild(card);
        });
    }

    function generateTask() {
        const randomTask = predefinedTasks[Math.floor(Math.random() * predefinedTasks.length)];
        const task = { name: randomTask, deadline: '', difficulty: 'Medium', category: 'Generated', completed: false, timeSpent: 0 };
        tasks.push(task);
        saveTasks();
        renderTasks();
    }

    document.getElementById('task-form').addEventListener('submit', function(e){
        e.preventDefault();
        const task = {
            name: document.getElementById('task-name').value,
            deadline: document.getElementById('task-deadline').value,
            difficulty: document.getElementById('task-difficulty').value,
            category: document.getElementById('task-category').value,
            completed: false,
            timeSpent: 0
        };
        tasks.push(task);
        saveTasks();
        renderTasks();
        this.reset();
    });

    function toggleTask(index) {
        tasks[index].completed = !tasks[index].completed;
        if(tasks[index].completed){
            let taskXP = tasks[index].difficulty === 'Easy' ? 10 : tasks[index].difficulty === 'Medium' ? 20 : 30;
            xp += taskXP;
            localStorage.setItem('xp', xp);
            level = Math.floor(xp / 100) + 1;
            document.getElementById('xp').innerText = xp;
            document.getElementById('level').innerText = level;
        }
        saveTasks();
        renderTasks();
    }

    let timers = {};
    function startTimer(index) {
        if(timers[index]) return;
        timers[index] = setInterval(()=>{
            tasks[index].timeSpent++;
            document.getElementById(`timer-${index}`).innerText = tasks[index].timeSpent;
            saveTasks();
        }, 60000);
    }

    renderTasks();
</script>

</body>
</html>
