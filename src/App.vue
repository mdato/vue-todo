<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
	todos.value.sort((a, b) => {
		return a.createdAt - b.createdAt;
	})
);

const todos_desc = computed(() =>
	todos.value.sort((a, b) => {
		return b.createdAt - a.createdAt;
	})
);

watch(name, (newVal) => {
	localStorage.setItem("name", newVal);
});

watch(
	todos,
	(newVal) => {
		localStorage.setItem("todos", JSON.stringify(newVal));
	},
	{
		deep: true,
	}
);

const addTodo = () => {
	if (input_content.value.trim() === "") {
		return;
	}

	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime(),
	});
};

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo);
};

onMounted(() => {
	name.value = localStorage.getItem("name") || "";
	todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
	<main class="app">
		<div class="ppal">
			<section class="greeting">
				<h1 class="title">
					To-Do list of:
					<input type="text" id="name" placeholder="put your name" v-model="name" />
				</h1>
			</section>

			<section class="create-todo">
				<form id="new-todo-form" @submit.prevent="addTodo">
					<input type="text" name="content" id="content" placeholder="e.g. go shopping"
						v-model="input_content" />

					<input type="submit" value="Add todo" />
				</form>
			</section>

			<section class="todo-list">
				<div class="list" id="todo-list">
					<div v-for="todo in todos_desc" :class="`todo-item ${todo.done && 'done'}`">
						<label>
							<input type="checkbox" v-model="todo.done" />
							<span :class="`bubble ${todo.category === ''}`"></span>
						</label>

						<div class="todo-content">
							<input type="text" v-model="todo.content" />
						</div>

						<div class="actions">
							<button class="delete" @click="removeTodo(todo)">X</button>
						</div>
					</div>
				</div>
			</section>
		</div>
	</main>
</template>
