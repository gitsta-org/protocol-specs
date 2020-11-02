import Datastore = require("nedb");
import { getServers } from "dns";

const gitsta: any = {};
// Must init git with a filesystem.
gitsta.init();

// NEDB database

// Set up NEDB
const db: any = {};
db.todos = new Datastore();
db.projects = new Datastore();

// This gets called when a user adds a todo.
// We simply add a new file to the file system.
async function addTodo(title: string, desc: string) {
  // Get a hyphenated path from the title. "Get Milk" => "todos/get-milk.json"
  const filepath = "todos/" + title.replace(/ +/g, "-").toLowerCase() + ".json";

  const todo = JSON.stringify({
    title,
    desc,
    completed: false,
  });

  const message = "Add a post";
  await git.commit(message, files: [
    {
      type: "ADD",
      filepath,
      todo,
    },
  ]);
}

// When a change occurs in the filesystem, this event fires.
gitsta.git.onChange(async (change: ChangeEvent) => {
  const [collection, filenameWithExtension] = change.path.split("/");
  const filename = filenameWithExtension.split(".")[0];

  if (collection === "todos") {
    if (change.type === "ADD") {
      processAddTodoEvent(filename, change.contents);
    }
  } else if (collection === "projects") {
    if (change.type === "ADD") {
      processAddProjectEvent(filename, change.contents);
    }
  }
});

async function processAddTodoEvent(filename: string, contents: string) {
  const todo = JSON.parse(contents);
  const doc = {
    __id: filename,
    title: todo.title,
    desc: todo.desc,
    completed: todo.completed,
  };

  db.todos.insert(doc);
}

async function processAddProjectEvent(filename: string, contents: string) {
  // gitsta.
}
