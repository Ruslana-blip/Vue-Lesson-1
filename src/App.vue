<template>
	<div class="flex">
		<div class="row">
			<h2>TO DO LIST</h2>
			<div class="row__action">
				<input type="text" v-model="newTodo" placeholder="Enter value" />
				<button @click="addItem" class="add">Add</button>
			</div>
			<div v-if="loading" class="loader"></div>
			<span v-if="errorMessage" class="error">{{ errorMessage }}</span>
			<ul class="list">
				<li v-for="todo in todoList" :key="todo.id" class="list__item">
					<input type="checkbox" :checked="todo.completed" />
					<span>{{ todo.title }}</span>
					<button @click="deleteTodo(todo.id)" class="remove">X</button>
				</li>
			</ul>
		</div>
	</div>
</template>

<script>
import axios from 'axios';
export default {
	data() {
		return {
			baseUrl: 'https://jsonplaceholder.typicode.com/',
			todoList: [],
			newTodo: '',
			errorMessage: '',
			loading: false,
		};
	},
	methods: {
		async addTodo() {
			this.loading = true;
			try {
				const { data } = await axios.get(`${this.baseUrl}todos`, {
					params: {
						_limit: 5,
					},
				});
				this.todoList = data;
				this.saveData();
			} catch (err) {
				this.errorMessage = 'Виникла помилка';
			} finally {
				this.loading = false;
			}
		},
		deleteTodo(id) {
			this.todoList = this.todoList.filter(item => item.id !== id);
			this.saveData();
		},
		addItem() {
			if (!this.newTodo) return;
			this.todoList.unshift({
				id: Date.now(),
				completed: false,
				title: this.newTodo,
			});
			this.newTodo = '';
			this.saveData();
		},
		saveData() {
			localStorage.setItem('list', JSON.stringify(this.todoList));
		},
		showData() {
			const data = JSON.parse(localStorage.getItem('list'));
			if (data && data.length) {
				this.todoList = data;
			} else {
				this.addTodo();
			}
		},
	},
	mounted() {
		this.showData();
	},
};
</script>

<style scoped lang="scss">
.flex {
	width: 800px;
	height: 100vh;
	margin: 0 auto;
	display: flex;
	justify-content: center;
	flex-direction: column;
}
.row {
	height: auto;
	border: 2px solid grey;
	border-radius: 40px;
	padding: 50px;
	box-shadow: 0px 3px 16px 7px grey;
	&__action {
		position: relative;
		margin-bottom: 40px;
	}
}

h2 {
	font-size: 40px;
	color: #454444;
}
input {
	height: 40px;
	width: 70%;
	border-radius: 30px;
	border: 1px solid #454444;
	font-size: 20px;
	padding: 10px;
}
.error {
	color: red;
	font-size: 30px;
}
.add {
	height: 66px;
	position: absolute;
	top: -4px;
	right: 0;
	width: 35%;
	border-radius: 30px;
	border: 1px solid #454444;
	background-color: orangered;
	font-size: 20px;
	color: aliceblue;
}

.list {
	& input {
		width: 40px;
		cursor: pointer;
	}
	// .list__item
	&__item {
		height: 40px;
		display: flex;
		align-items: center;
		justify-content: space-between;
		list-style: none;
		&:not(:last-child) {
			margin-bottom: 10px;
		}
		& span {
			font-size: 20px;
		}
	}
}
.remove {
	width: 40px;
	height: 40px;
	align-self: center;
	cursor: pointer;
}

//

.loader {
	width: calc(6 * 30px);
	height: 50px;
	display: flex;
	color: #8d7958;
	filter: drop-shadow(30px 25px 0 currentColor)
		drop-shadow(60px 0 0 currentColor) drop-shadow(120px 0 0 currentColor);
	clip-path: inset(0 100% 0 0);
	animation: l12 2s infinite steps(7);
	margin-bottom: 40px;
}
.loader:before {
	content: '';
	width: 30px;
	height: 25px;
	--c: no-repeat radial-gradient(farthest-side, currentColor 92%, #0000);
	background: var(--c) left / 70% 70%, var(--c) right/20% 20%,
		var(--c) top 0 right 15%/20% 20%, var(--c) bottom 0 right 15%/20% 20%;
}
@keyframes l12 {
	100% {
		clip-path: inset(0 -30px 0 0);
	}
}
</style>
