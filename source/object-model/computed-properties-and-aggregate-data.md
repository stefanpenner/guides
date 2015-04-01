Often, you may have a computed property that relies on all of the items in an
array to determine its value. For example, you may want to count all of the
todo items in a controller to determine how many of them are completed.

Here's what that computed property might look like:

```app/controllers/todos.js
export default Ember.Controller.extend({
  init: function() {
    this._super.apply(this, arguments);
    this.set('todos', [
      { isDone: true  },
      { isDone: false },
      { isDone: true  }
    ]);
  },

  remaining: Ember.computed('todos.@each.isDone', function() {
    var todos = this.get('todos');
    return todos.filterBy('isDone', false).get('length');
  })
});
```

Note here that the dependent key (`todos.@each.isDone`) contains the special
key `@each`. This instructs Ember.js to update bindings and fire observers for
this computed property when one of the following four events occurs:

1. The `isDone` property of any of the objects in the `todos` array changes.
2. An item is added to the `todos` array.
3. An item is removed from the `todos` array.
4. The `todos` property of the controller is changed to a different array.

In the example above, the `remaining` count is `1`:

```javascript
import TodosController from 'app/controllers/todos';
todosController = TodosController.create();
todosController.get('remaining');
// 1
```

If we change the todo's `isDone` property, the `remaining` property is updated
automatically:

```javascript
var todos = todosController.get('todos');
var todo = todos.objectAt(1);
todo.set('isDone', true);

todosController.get('remaining');
// 0

todo = Ember.Object.create({ isDone: false });
todos.pushObject(todo);

todosController.get('remaining');
// 1
```

Note that `@each` only works one level deep. You cannot use nested forms like
`todos.@each.owner.name` or `todos.@each.owner.@each.name`.
