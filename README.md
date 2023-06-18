# node-workflow

node-workflow is an node js workflow composition module. The idea is to be able to define workflows in your node repos in a declarative method. Your workflow consists of tasks that use the functions / methods defined in your node js code. You can define sequential or parallel executions of tasks, and use the outut of one task as input into another. For example, 

async function helloTask(name) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(`${new Date().toISOString()}: hello $name`);
    }, 1000)
  })
}

A simple workflow could be (in JSON):

{
   workflow: "Simple Workflow",
   schedule: "* * * * 10",
   tasks: {
        task1: {
          ref: helloTask,
          args: "Rafay"
        },
        task2: {
            ref: helloTask,
            args: "Ahmad"
        },
        task3: {
            ref: helloTask,
            args: "Avais",
            after: ['task1']
        },
        task4: {
            ref: helloTask,
            args: "Noor",
            after: ['task2', 'task3']
        }
   }
}

Tasks 1 and 2 have no "after" dependencies and therefore run right when the workflow starts. The expected could be:

timestamp 1: Hello Rafay ---> task1 at Timestamp 1
timestamp 1: Hello Noor  ---> task2 at Timestamp 1 or near that
timestamp 2: Hello Avais ---> task3 runs after task 1
timestamp 3: Hello Noor  ---> task4 runs after task2 and task 3 both complete
