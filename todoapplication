const express = require("express");
const path = require("path");
const { open } = require("sqlite");
const sqlite3 = require("sqlite3");
const format = require("date-fns/format");
const isMatch = require("date-fns/isMatch");
var isValid = require("date-fns/isValid");
const app = express();
app.use(express.json());

let database;
const initializeDBandServer = async () => {
  try {
    database = await open({
      filename: path.join(__dirname, "todoApplication.db"),
      driver: sqlite3.Database,
    });
    app.listen(3000, () => {
      console.log("Server is running on http://localhost:3000/");
    });
  } catch (error) {
    console.log(`DataBase error is ${error.message}`);
    process.exit(1);
  }
};
initializeDBandServer();

//get the list of todos
/*
app.get("/todos/", async (request, response) => {
  const requestQuery = `select * from todo;`;
  const responseResult = await database.all(requestQuery);
  response.send(responseResult);
});
*/

//api 1

const hasPriorityAndStatusProperties = (requestQuery) => {
  return (
    requestQuery.priority !== undefined && requestQuery.status !== undefined
  );
};

const hasPriorityProperty = (requestQuery) => {
  return requestQuery.priority !== undefined;
};

const hasStatusProperty = (requestQuery) => {
  return requestQuery.status !== undefined;
};

const hasCategoryAndStatus = (requestQuery) => {
  return (
    requestQuery.category !== undefined && requestQuery.status !== undefined
  );
};

const hasCategoryAndPriority = (requestQuery) => {
  return (
    requestQuery.category !== undefined && requestQuery.priority !== undefined
  );
};

const hasSearchProperty = (requestQuery) => {
  return requestQuery.search_q !== undefined;
};

const hasCategoryProperty = (requestQuery) => {
  return requestQuery.category !== undefined;
};

const outPutResult = (dbObject) => {
  return {
    id: dbObject.id,
    todo: dbObject.todo,
    priority: dbObject.priority,
    category: dbObject.category,
    status: dbObject.status,
    dueDate: dbObject.due_date,
  };
};

app.get("/todos/", async (request, response) => {
  let data = null;
  let getTodosQuery = "";
  const { search_q = "", priority, status, category } = request.query;
  /*console.log(hasPriorityAndStatusProperties(request.query));
  console.log(hasCategoryAndStatus(request.query));
  console.log(hasCategoryAndPriority(request.query));
  console.log(hasPriorityProperty(request.query));
  console.log(hasStatusProperty(request.query));
  console.log(hasCategoryProperty(request.query));
  console.log(hasSearchProperty(request.query));*/

  /** switch case  */
  switch (true) {
    //scenario 3
    /**----------- has priority and status -------- */
    case hasPriorityAndStatusProperties(request.query):
      if (priority === "HIGH" || priority === "MEDIUM" || priority === "LOW") {
        if (
          status === "TO DO" ||
          status === "IN PROGRESS" ||
          status === "DONE"
        ) {
          getTodosQuery = `
      SELECT * FROM todo  WHERE status = '${status}' AND priority = '${priority}';`;
          data = await database.all(getTodosQuery);
          response.send(data.map((eachItem) => outPutResult(eachItem)));
        } else {
          response.status(400);
          response.send("Invalid Todo Status");
        }
      } else {
        response.status(400);
        response.send("Invalid Todo Priority");
      }

      break;

    //scenario 5
    /** has  category and status  */
    case hasCategoryAndStatus(request.query):
      if (
        category === "WORK" ||
        category === "HOME" ||
        category === "LEARNING"
      ) {
        if (
          status === "TO DO" ||
          status === "IN PROGRESS" ||
          status === "DONE"
        ) {
          getTodosQuery = `select * from todo where category='${category}' and status='${status}';`;
          data = await database.all(getTodosQuery);
          response.send(data.map((eachItem) => outPutResult(eachItem)));
        } else {
          response.status(400);
          response.send("Invalid Todo Status");
        }
      } else {
        response.status(400);
        response.send("Invalid Todo Category");
      }

      break;

    //scenario 7
    /** has both category and priority */
    case hasCategoryAndPriority(request.query):
      if (
        category === "WORK" ||
        category === "HOME" ||
        category === "LEARNING"
      ) {
        if (
          priority === "HIGH" ||
          priority === "MEDIUM" ||
          priority === "LOW"
        ) {
          getTodosQuery = `select * from todo where category='${category}' and priority='${priority}';`;
          data = await database.all(getTodosQuery);
          response.send(data.map((eachItem) => outPutResult(eachItem)));
        } else {
          response.status(400);
          response.send("Invalid Todo Priority");
        }
      } else {
        response.status(400);
        response.send("Invalid Todo Category");
      }

      break;

    //scenario 2
    /**-------------- has only priority---------- */
    case hasPriorityProperty(request.query):
      if (priority === "HIGH" || priority === "MEDIUM" || priority === "LOW") {
        getTodosQuery = `
      SELECT * FROM todo WHERE priority = '${priority}';`;
        data = await database.all(getTodosQuery);
        response.send(data.map((eachItem) => outPutResult(eachItem)));
      } else {
        response.status(400);
        response.send("Invalid Todo Priority");
      }
      break;

    //scenario 1
    /**-------------has only status ------------ */
    case hasStatusProperty(request.query):
      if (status === "TO DO" || status === "IN PROGRESS" || status === "DONE") {
        getTodosQuery = `SELECT * FROM todo WHERE status = '${status}';`;
        data = await database.all(getTodosQuery);
        response.send(data.map((eachItem) => outPutResult(eachItem)));
      } else {
        response.status(400);
        response.send("Invalid Todo Status");
      }
      break;
    //has only search property
    //scenario 4
    case hasSearchProperty(request.query):
      getTodosQuery = `select * from todo where todo like '%${search_q}%';`;
      data = await database.all(getTodosQuery);
      response.send(data.map((eachItem) => outPutResult(eachItem)));
      break;
    //scenario 6
    //has only category
    case hasCategoryProperty(request.query):
      if (
        category === "WORK" ||
        category === "HOME" ||
        category === "LEARNING"
      ) {
        getTodosQuery = `select * from todo where category='${category}';`;
        data = await database.all(getTodosQuery);
        response.send(data.map((eachItem) => outPutResult(eachItem)));
      } else {
        response.status(400);
        response.send("Invalid Todo Category");
      }
      break;

    //default get all todos
    default:
      getTodosQuery = `select * from todo;`;
      data = await database.all(getTodosQuery);
      response.send(data.map((eachItem) => outPutResult(eachItem)));
  }
});

//api2
app.get("/todos/:todoId/", async (request, response) => {
  const { todoId } = request.params;
  const getToDoQuery = `select * from todo where id=${todoId};`;
  const responseResult = await database.get(getToDoQuery);
  response.send(outPutResult(responseResult));
});

//api3
app.get("/agenda/", async (request, response) => {
  const { date } = request.query;
  console.log(isMatch(date, "yyyy-MM-dd"));
  if (isMatch(date, "yyyy-MM-dd")) {
    const newDate = format(new Date(date), "yyyy-MM-dd");
    console.log(newDate);
    const requestQuery = `select * from todo where due_date='${newDate}';`;
    const responseResult = await database.all(requestQuery);
    //console.log(responseResult);
    response.send(responseResult.map((eachItem) => outPutResult(eachItem)));
  } else {
    response.status(400);
    response.send("Invalid Due Date");
  }
});

//api4
app.post("/todos/", async (request, response) => {
  const { id, todo, priority, status, category, dueDate } = request.body;
  if (priority === "HIGH" || priority === "LOW" || priority === "MEDIUM") {
    if (status === "TO DO" || status === "IN PROGRESS" || status === "DONE") {
      if (
        category === "WORK" ||
        category === "HOME" ||
        category === "LEARNING"
      ) {
        if (isMatch(dueDate, "yyyy-MM-dd")) {
          const postNewDueDate = format(new Date(dueDate), "yyyy-MM-dd");
          const postTodoQuery = `
  INSERT INTO
    todo (id, todo, category,priority, status, due_date)
  VALUES
    (${id}, '${todo}', '${category}','${priority}', '${status}', '${postNewDueDate}');`;
          await database.run(postTodoQuery);
          //console.log(responseResult);
          response.send("Todo Successfully Added");
        } else {
          response.status(400);
          response.send("Invalid Due Date");
        }
      } else {
        response.status(400);
        response.send("Invalid Todo Category");
      }
    } else {
      response.status(400);
      response.send("Invalid Todo Status");
    }
  } else {
    response.status(400);
    response.send("Invalid Todo Priority");
  }
});

//api5
app.put("/todos/:todoId/", async (request, response) => {
  const { todoId } = request.params;
  let updateColumn = "";
  const requestBody = request.body;
  console.log(requestBody);
  const previousTodoQuery = `SELECT * FROM todo WHERE id = ${todoId};`;
  const previousTodo = await database.get(previousTodoQuery);
  const {
    todo = previousTodo.todo,
    priority = previousTodo.priority,
    status = previousTodo.status,
    category = previousTodo.category,
    dueDate = previousTodo.dueDate,
  } = request.body;

  let updateTodoQuery;
  switch (true) {
    // update status
    case requestBody.status !== undefined:
      if (status === "TO DO" || status === "IN PROGRESS" || status === "DONE") {
        updateTodoQuery = `
    UPDATE todo SET todo='${todo}', priority='${priority}', status='${status}', category='${category}',
     due_date='${dueDate}' WHERE id = ${todoId};`;

        await database.run(updateTodoQuery);
        response.send(`Status Updated`);
      } else {
        response.status(400);
        response.send("Invalid Todo Status");
      }
      break;

    //update priority
    case requestBody.priority !== undefined:
      if (priority === "HIGH" || priority === "LOW" || priority === "MEDIUM") {
        updateTodoQuery = `
    UPDATE todo SET todo='${todo}', priority='${priority}', status='${status}', category='${category}',
     due_date='${dueDate}' WHERE id = ${todoId};`;

        await database.run(updateTodoQuery);
        response.send(`Priority Updated`);
      } else {
        response.status(400);
        response.send("Invalid Todo Priority");
      }
      break;

    //update todo
    case requestBody.todo !== undefined:
      updateTodoQuery = `
    UPDATE todo SET todo='${todo}', priority='${priority}', status='${status}', category='${category}',
     due_date='${dueDate}' WHERE id = ${todoId};`;

      await database.run(updateTodoQuery);
      response.send(`Todo Updated`);
      break;

    //update category
    case requestBody.category !== undefined:
      if (
        category === "WORK" ||
        category === "HOME" ||
        category === "LEARNING"
      ) {
        updateTodoQuery = `
    UPDATE todo SET todo='${todo}', priority='${priority}', status='${status}', category='${category}',
     due_date='${dueDate}' WHERE id = ${todoId};`;

        await database.run(updateTodoQuery);
        response.send(`Category Updated`);
      } else {
        response.status(400);
        response.send("Invalid Todo Category");
      }
      break;
    //update due date
    case requestBody.dueDate !== undefined:
      if (isMatch(dueDate, "yyyy-MM-dd")) {
        const newDueDate = format(new Date(dueDate), "yyyy-MM-dd");
        updateTodoQuery = `
    UPDATE todo SET todo='${todo}', priority='${priority}', status='${status}', category='${category}',
     due_date='${newDueDate}' WHERE id = ${todoId};`;

        await database.run(updateTodoQuery);
        response.send(`Due Date Updated`);
      } else {
        response.status(400);
        response.send("Invalid Due Date");
      }
      break;
  }

  /*updateTodoQuery = `
    UPDATE todo SET todo='${todo}', priority='${priority}', status='${status}', category='${category}',
     due_date='${dueDate}' WHERE id = ${todoId};`;
  const responseData = await database.run(updateTodoQuery);
  response.send(`${updateColumn} Updated`);*/
});

//api6

app.delete("/todos/:todoId/", async (request, response) => {
  const { todoId } = request.params;
  const deleteTodoQuery = `
  DELETE FROM
    todo
  WHERE
    id = ${todoId};`;

  await database.run(deleteTodoQuery);
  response.send("Todo Deleted");
});

module.exports = app;

// /*
//  *  Created a Table with name todo in the todoApplication.db file using the CLI.
//  */

// const express = require("express");
// const { open } = require("sqlite");
// const sqlite3 = require("sqlite3");
// const path = require("path");
// const format = require("date-fns/format");
// const databasePath = path.join(__dirname, "todoApplication.db");
// const isValid = require("date-fns/isValid");
// const app = express();

// app.use(express.json());

// let database = null;

// const initializeDbAndServer = async () => {
//   try {
//     database = await open({
//       filename: databasePath,
//       driver: sqlite3.Database,
//     });

//     app.listen(3000, () =>
//       console.log("Server Running at http://localhost:3000/")
//     );
//   } catch (error) {
//     console.log(`DB Error: ${error.message}`);
//     process.exit(1);
//   }
// };

// initializeDbAndServer();
// // const invalidScenariosInQuery = (request, response, next) => {
// //   const { status, priority, category, date } = request.query;
// //   if (
// //     status !== "TO DO" &&
// //     status !== "IN PROGRESS" &&
// //     status !== "DONE" &&
// //     status !== undefined
// //   ) {
// //     response.status(400);
// //     response.send("Invalid Todo Status");
// //   }
// //   if (
// //     priority !== "LOW" &&
// //     priority !== "MEDIUM" &&
// //     priority !== "HIGH" &&
// //     priority !== undefined
// //   ) {
// //     response.status(400);
// //     response.send("Invalid Todo Priority");
// //   }

// //   if (
// //     category !== "HOME" &&
// //     category !== "LEARNING" &&
// //     category !== "WORK" &&
// //     category !== undefined
// //   ) {
// //     response.status(400);
// //     response.send("Invalid Todo Category");
// //   }
// //   if (isValid(date) === false && date !== undefined) {
// //     response.status(400);
// //     response.send("Invalid Todo Due Date");
// //   }
// // };
// // let hasValidStatus = (status) =>
// //   status === "TO DO" || status === "IN PROGRESS" || status === "DONE";
// // let hasValidPriority = (priority) =>
// //   priority === "LOW" || priority === "HIGH" || priority === "MEDIUM";
// // let hasValidCategory = (category) =>
// //   category === "TO DO" || category === "IN PROGRESS" || category === "DONE";
// // let hasValidDate = (date) => isValid(date);

// // const invalidScenarios = (request, response, next) => {
// //   const { status, category, priority } = request.query;
// //   const validStatus = (status) => allStatus.includes(status);
// //   const validPriority = (priority) => allStatus.includes(priority);
// //   const validCategory = (category) => allStatus.includes(category);
// //   if (validCategory(category) === false) {
// //     response.status(400);
// //     response.send("Invalid Todo Category");
// //   }
// //   if (validPriority(priority) === false) {
// //     response.status(400);
// //     response.send("Invalid Todo Priority");
// //   }
// //   if (validStatus(status) === false) {
// //     response.status(400);
// //     response.send("Invalid Todo Status");
// //   }
// //   if (
// //     (validCategory(category) === true) |
// //     (validPriority(priority) === true) |
// //     (validStatus(status) === true)
// //   ) {
// //     next();
// //   }
// // };

// const hasPriorityAndStatusProperties = (requestQuery) => {
//   return (
//     requestQuery.priority !== undefined && requestQuery.status !== undefined
//   );
// };
// const hasCategoryAndStatusProperties = (requestQuery) => {
//   return (
//     requestQuery.category !== undefined && requestQuery.status !== undefined
//   );
// };
// const hasCategoryAndPriorityProperties = (requestQuery) => {
//   return (
//     requestQuery.category !== undefined && requestQuery.priority !== undefined
//   );
// };

// const hasPriorityProperty = (requestQuery) => {
//   return requestQuery.priority !== undefined;
// };
// const hasCategoryProperty = (requestQuery) => {
//   return requestQuery.category !== undefined;
// };

// const hasStatusProperty = (requestQuery) => {
//   return requestQuery.status !== undefined;
// };
// // const invalidSce = (request, response, next) => {
// //   const { priority, status, category } = request.query;

// //   ALLOWED_STATUSES = ["TO DO", "IN PROGRESS", "DONE", undefined];
// //   ALLOWED_PRIORITIES = ["LOW", "MEDIUM", "HIGH", undefined];
// //   ALLOWED_CATEGORIES = ["HOME", "WORK", "LEARNING", undefined];
// //   if (!ALLOWED_PRIORITIES.includes(priority)) {
// //     return res.status(400).send("Invalid Todo Priority");
// //   }

// //   if (!ALLOWED_STATUSES.includes(status)) {
// //     return res.status(400).send("Invalid Todo Status");
// //   }

// //   if (!ALLOWED_CATEGORIES.includes(category)) {
// //     return res.status(400).send("Invalid Todo Category");
// //   }
// // };

// //   if (!dateFns.isValid(dueDate)) {
// //     return res.status(400).send("Invalid Due Date");
// //   }

// app.get("/todos/", async (request, response) => {
//   let data = null;
//   let getTodosQuery = "";
//   const { search_q = "", priority, status, category, date } = request.query;

//   switch (true) {
//     case hasPriorityAndStatusProperties(request.query):
//       getTodosQuery = `
//       SELECT
//         id,todo,priority,status,category,due_date as dueDate
//       FROM
//         todo
//       WHERE
//         todo LIKE '%${search_q}%'
//         AND status = '${status}'
//         AND priority = '${priority}';`;
//       break;

//     case hasCategoryAndStatusProperties(request.query):
//       getTodosQuery = `
//       SELECT
//         id,todo,priority,status,category,due_date as dueDate
//       FROM
//         todo
//       WHERE
//         todo LIKE '%${search_q}%'
//         AND status = '${status}'
//         AND category = '${category}';`;
//       break;

//     case hasCategoryAndPriorityProperties(request.query):
//       getTodosQuery = `
//       SELECT
//         id,todo,priority,status,category,due_date as dueDate
//       FROM
//         todo
//       WHERE
//         todo LIKE '%${search_q}%'
//         AND category = '${category}'
//         AND priority = '${priority}';`;
//       break;

//     case hasPriorityProperty(request.query):
//       getTodosQuery = `
//       SELECT
//         id,todo,priority,status,category,due_date as dueDate
//       FROM
//         todo
//       WHERE
//         todo LIKE '%${search_q}%'
//         AND priority = '${priority}';`;
//       break;

//     case hasStatusProperty(request.query):
//       getTodosQuery = `
//       SELECT
//         id,todo,priority,status,category,due_date as dueDate
//       FROM
//         todo
//       WHERE
//         todo LIKE '%${search_q}%'
//         AND status = '${status}';`;
//       break;

//     case hasCategoryProperty(request.query):
//       getTodosQuery = `
//       SELECT
//         id,todo,priority,status,category,due_date as dueDate
//       FROM
//         todo
//       WHERE
//         todo LIKE '%${search_q}%'
//         AND category = '${category}';`;
//       break;

//     default:
//       getTodosQuery = `
//       SELECT
//         id,todo,priority,status,category,due_date as dueDate
//       FROM
//         todo
//       WHERE
//         todo LIKE '%${search_q}%';`;
//   }

//   data = await database.all(getTodosQuery);
//   response.send(data);
// });
// //API2
// app.get("/todos/:todoId/", async (request, response) => {
//   const { todoId } = request.params;

//   const getTodoQuery = `
//     SELECT
//       id,todo,priority,status,category,due_date as dueDate
//     FROM
//       todo
//     WHERE
//       id = ${todoId};`;
//   const todo = await database.get(getTodoQuery);
//   response.send(todo);
// });

// //API3

// app.get("/agenda/", async (request, response) => {
//   const { date } = request.query;
//   const gDate = new Date(date);
//   const newDate = format(gDate, "yyyy-MM-dd");

//   const getAllActivity = `SELECT
//   id,todo,priority,status,category,due_date as dueDate
//    FROM todo
//   WHERE due_date= '${newDate}' `;
//   const res = await database.all(getAllActivity);
//   response.send(res);
// });
// //API4

// app.post("/todos/", async (request, response) => {
//   const { id, todo, priority, status, category, dueDate } = request.body;
//   const postTodoQuery = `
//   INSERT INTO
//     todo (id, todo, priority, status,category,due_date)
//   VALUES
//     (${id}, '${todo}', '${priority}', '${status}','${category}','${dueDate}');`;
//   await database.run(postTodoQuery);
//   response.send("Todo Successfully Added");
// });
// app.put("/todos/:todoId/", async (request, response) => {
//   const { todoId } = request.params;
//   let updateColumn = "";
//   const requestBody = request.body;
//   switch (true) {
//     case requestBody.status !== undefined:
//       updateColumn = "Status";
//       break;
//     case requestBody.priority !== undefined:
//       updateColumn = "Priority";
//       break;
//     case requestBody.todo !== undefined:
//       updateColumn = "Todo";
//       break;
//     case requestBody.dueDate !== undefined:
//       updateColumn = "Due Date";
//       break;
//     case requestBody.category !== undefined:
//       updateColumn = "Category";
//       break;
//   }
//   const previousTodoQuery = `
//     SELECT
//       *
//     FROM
//       todo
//     WHERE
//       id = ${todoId};`;
//   const previousTodo = await database.get(previousTodoQuery);

//   const {
//     todo = previousTodo.todo,
//     priority = previousTodo.priority,
//     status = previousTodo.status,
//     category = previousTodo.category,
//     dueDate = previousTodo.dueDate,
//   } = request.body;

//   const updateTodoQuery = `
//     UPDATE
//       todo
//     SET
//       todo='${todo}',
//       priority='${priority}',
//       status='${status}',
//       category='${category}',
//       due_date='${dueDate}'
//     WHERE
//       id = ${todoId};`;

//   await database.run(updateTodoQuery);
//   response.send(`${updateColumn} Updated`);
// });

// app.delete("/todos/:todoId/", async (request, response) => {
//   const { todoId } = request.params;
//   const deleteTodoQuery = `
//   DELETE FROM
//     todo
//   WHERE
//     id = ${todoId};`;

//   await database.run(deleteTodoQuery);
//   response.send("Todo Deleted");
// });

// module.exports = app;
