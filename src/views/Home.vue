<template>
	<div>
		<AddTask v-show="showAddTask" @add-task="addTask" />
		<Tasks
			@toggle-reminder="toggleReminder"
			@delete-task="deleteTask"
			:tasks="tasks"
		/>
	</div>
</template>

<script>
import AddTask from '../components/AddTask.vue';
import Tasks from '../components/Tasks.vue';
export default {
	name: 'Home',
	props: {
		showAddTask: Boolean,
	},
	components: {
		Tasks,
		AddTask,
	},
	data() {
		return {
			tasks: [],
		};
	},
	methods: {
		toogleAdddTask() {
			this.showAddTask = !this.showAddTask;
		},
		async addTask(task) {
			const res = await fetch('api/tasks', {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json',
				},
				body: JSON.stringify(task),
			});

			const data = await res.json();

			this.tasks = [...this.tasks, data];
		},
		async deleteTask(id) {
			if (confirm('Are you sure?')) {
				const res = await fetch(`api/tasks/${id}`, {
					method: 'DELETE',
				});
				res.status === 200
					? (this.tasks = this.tasks.filter((tasks) => tasks.id !== id))
					: alert('Error deleting task');
			}
		},
		async toggleReminder(id) {
			const taskToToggle = await this.fetchTask(id);
			const updateTask = { ...taskToToggle, reminder: !taskToToggle.reminder };

			const res = await fetch(`api/tasks/${id}`, {
				method: 'PUT',
				headers: {
					'Content-Type': 'application/json',
				},
				body: JSON.stringify(updateTask),
			});

			const data = await res.json();

			this.tasks = this.tasks.map((task) =>
				task.id === id ? { ...task, reminder: data.reminder } : task
			);
		},
		async fetchTasks() {
			const res = await fetch('api/tasks');

			const data = await res.json();

			return data;
		},
		async fetchTask(id) {
			const res = await fetch(`api/tasks/${id}`);

			const data = await res.json();

			return data;
		},
	},
	//life cycle method commonly used to make a request and get the data
	async created() {
		this.tasks = await this.fetchTasks();
	},
};
</script>
