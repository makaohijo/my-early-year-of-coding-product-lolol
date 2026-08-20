# my-early-year-of-coding-product-lolol
2020
<!DOCTYPE html>
<html>
<head>
    <title>My Discipline Planner</title>

    <style>
        body {
            font-family: Arial;
            background: #111;
            color: white;
            max-width: 900px;
            margin: auto;
            padding: 30px;
        }

        h1 {
            font-size: 40px;
        }

        .card {
            background: #1c1c1c;
            padding: 20px;
            margin-bottom: 20px;
            border-radius: 15px;
        }

        input, select {
            padding: 10px;
            margin: 5px;
            border-radius: 7px;
            border: none;
        }

        button {
            padding: 10px 15px;
            margin: 5px;
            border: none;
            border-radius: 7px;
            cursor: pointer;
        }

        .add {
            background: white;
            color: black;
        }

        .complete {
            background: #333;
            color: white;
        }

        .calendar {
            background: #244a7c;
            color: white;
        }

        .delete {
            background: #5c2020;
            color: white;
        }

        .task {
            border-top: 1px solid #444;
            padding: 15px 0;
        }

        .done {
            text-decoration: line-through;
            opacity: 0.5;
        }
    </style>
</head>

<body>

<h1>My Discipline Planner</h1>

<p id="date"></p>

<div class="card">

    <h2>Add Task</h2>

    <input
        id="task"
        placeholder="What do you need to do?"
    >

    <select id="category">
        <option>University</option>
        <option>FYP</option>
        <option>Career</option>
        <option>Engineering Skills</option>
        <option>Exercise</option>
        <option>Personal</option>
    </select>

    <input
        id="deadline"
        type="datetime-local"
    >

    <select id="priority">
        <option>Critical</option>
        <option>High</option>
        <option selected>Medium</option>
        <option>Low</option>
    </select>

    <select id="reminder">
        <option value="1440">1 Day Before</option>
        <option value="60">1 Hour Before</option>
        <option value="30">30 Minutes Before</option>
        <option value="15" selected>15 Minutes Before</option>
        <option value="10">10 Minutes Before</option>
    </select>

    <button class="add" onclick="addTask()">
        Add Task
    </button>

</div>


<div class="card">

    <h2>My Tasks</h2>

    <div id="taskList"></div>

</div>


<script>

document.getElementById("date").innerText =
    new Date().toDateString();


let tasks =
    JSON.parse(localStorage.getItem("tasks")) || [];


function saveTasks() {

    localStorage.setItem(
        "tasks",
        JSON.stringify(tasks)
    );

}


function addTask() {

    let name =
        document.getElementById("task").value;

    let category =
        document.getElementById("category").value;

    let deadline =
        document.getElementById("deadline").value;

    let priority =
        document.getElementById("priority").value;

    let reminder =
        document.getElementById("reminder").value;


    if (name === "") {

        alert("Please enter a task.");

        return;

    }


    let newTask = {

        name: name,

        category: category,

        deadline: deadline,

        priority: priority,

        reminder: reminder,

        completed: false

    };


    tasks.push(newTask);

    saveTasks();

    showTasks();


    document.getElementById("task").value = "";

}


function completeTask(index) {

    tasks[index].completed =
        !tasks[index].completed;

    saveTasks();

    showTasks();

}


function deleteTask(index) {

    tasks.splice(index, 1);

    saveTasks();

    showTasks();

}


function formatICSDate(date) {

    let year = date.getFullYear();

    let month =
        String(date.getMonth() + 1).padStart(2, "0");

    let day =
        String(date.getDate()).padStart(2, "0");

    let hour =
        String(date.getHours()).padStart(2, "0");

    let minute =
        String(date.getMinutes()).padStart(2, "0");

    let second = "00";


    return (
        year +
        month +
        day +
        "T" +
        hour +
        minute +
        second
    );

}


function addToCalendar(index) {

    let task = tasks[index];


    if (!task.deadline) {

        alert("Please set a date and time first.");

        return;

    }


    let start =
        new Date(task.deadline);


    let end =
        new Date(
            start.getTime() +
            60 * 60 * 1000
        );


    let reminder =
        task.reminder || 15;


    let ics =

`BEGIN:VCALENDAR
VERSION:2.0
PRODID:-//My Discipline Planner//EN
BEGIN:VEVENT
UID:${Date.now()}@disciplineplanner
DTSTART:${formatICSDate(start)}
DTEND:${formatICSDate(end)}
SUMMARY:${task.name}
DESCRIPTION:Category: ${task.category} | Priority: ${task.priority}
BEGIN:VALARM
ACTION:DISPLAY
DESCRIPTION:Reminder - ${task.name}
TRIGGER:-PT${reminder}M
END:VALARM
END:VEVENT
END:VCALENDAR`;


    let blob =
        new Blob(
            [ics],
            {
                type: "text/calendar"
            }
        );


    let link =
        document.createElement("a");


    link.href =
        URL.createObjectURL(blob);


    link.download =
        task.name.replaceAll(" ", "_") +
        ".ics";


    link.click();


    URL.revokeObjectURL(link.href);

}


function showTasks() {

    let list =
        document.getElementById("taskList");


    list.innerHTML = "";


    tasks.forEach(function(task, index) {

        let div =
            document.createElement("div");


        div.className =
            task.completed
            ? "task done"
            : "task";


        div.innerHTML = `

            <h3>${task.name}</h3>

            <p>
                Category:
                ${task.category}
            </p>

            <p>
                Priority:
                ${task.priority}
            </p>

            <p>
                Deadline:
                ${task.deadline || "None"}
            </p>

            <p>
                Reminder:
                ${task.reminder || 15} minutes before
            </p>

            <button
                class="complete"
                onclick="completeTask(${index})"
            >
                Complete
            </button>

            <button
                class="calendar"
                onclick="addToCalendar(${index})"
            >
                Add to Calendar
            </button>

            <button
                class="delete"
                onclick="deleteTask(${index})"
            >
                Delete
            </button>

        `;


        list.appendChild(div);

    });

}


showTasks();

</script>

</body>
</html>
