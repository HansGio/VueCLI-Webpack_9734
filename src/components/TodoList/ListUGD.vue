<template>
	<v-main class="list">
		<h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>

		<v-card>
			<v-card-title>
				<v-text-field
					v-model="search"
					append-icon="mdi-magnify"
					label="Search"
					single-line
					hide-details
				></v-text-field>
				<v-spacer></v-spacer>
				<v-btn class="mr-3" color="purple" dark @click="dialogFinished = true">
					Todo Selesai
				</v-btn>
				<v-btn color="success" dark @click="dialog = true">
					Tambah
				</v-btn>
			</v-card-title>
			<v-data-table :headers="headers" :items="todos" :search="search">
				<template v-slot:[`item.priority`]="{ item }">
					<v-chip outlined label :color="getColor(item.priority)" dark>
						{{ item.priority }}
					</v-chip>
				</template>
				<template v-slot:[`item.actions`]="{ item }">
					<v-icon color="blue" small class="mr-2" @click="editItem(item)">
						mdi-pencil
					</v-icon>
					<v-icon color="red" small @click="deleteItem(item)">
						mdi-delete
					</v-icon>
				</template>
			</v-data-table>
		</v-card>

		<v-dialog v-model="dialog" persistent max-width="600px">
			<v-card>
				<v-card-title>
					<span class="headline">Form Todo</span>
				</v-card-title>
				<v-card-text>
					<v-container>
						<v-text-field v-model="formTodo.task" label="Task" required></v-text-field>

						<v-select
							v-model="formTodo.priority"
							:items="['Penting', 'Biasa', 'Tidak penting']"
							label="Priority"
							required
						></v-select>

						<v-textarea v-model="formTodo.note" label="Note" required></v-textarea>
					</v-container>
				</v-card-text>
				<v-card-actions>
					<v-spacer></v-spacer>
					<v-btn color="blue darken-1" text @click="cancel">
						Cancel
					</v-btn>
					<v-btn color="blue darken-1" text @click="save">
						Save
					</v-btn>
				</v-card-actions>
			</v-card>
		</v-dialog>

		<v-dialog v-model="dialogDelete" persistent max-width="350px">
			<v-card>
				<v-card-title>
					<span class="headline font-weight-bold">Yakin ingin menghapus?</span>
				</v-card-title>
				<v-card-actions>
					<v-spacer></v-spacer>
					<v-spacer></v-spacer>
					<v-btn color="green darken-1" text @click="cancelDelete">
						Tidak
					</v-btn>
					<v-btn color="red darken-1" text @click="confirmDelete">
						Ya
					</v-btn>
				</v-card-actions>
			</v-card>
		</v-dialog>

		<v-dialog v-model="dialogFinished" persistent max-width="1000px">
			<v-card>
				<v-card-title>
					<span class="headline font-weight-bold">Finished To do</span>
				</v-card-title>
				<v-card-text>
					<v-container>
						<v-data-table :headers="headersFinished" :items="finishedTodos">
							<template v-slot:[`item.priority`]="{ item }">
								<v-chip outlined label :color="getColor(item.priority)" dark>
									{{ item.priority }}
								</v-chip>
							</template>
						</v-data-table>
					</v-container>
				</v-card-text>
				<v-card-actions>
					<v-spacer></v-spacer>
					<v-btn color="blue darken-1" text @click="dialogFinished = false">
						Close
					</v-btn>
				</v-card-actions>
			</v-card>
		</v-dialog>
	</v-main>
</template>
<script>
export default {
	name: "List",
	data() {
		return {
			search: null,
			dialog: false,
			dialogDelete: false,
			dialogFinished: false,
			editIndex: -1,
			headers: [
				{
					text: "Task",
					align: "start",
					sortable: true,
					value: "task",
				},
				{ text: "Priority", value: "priority" },
				{ text: "Note", value: "note" },
				{ text: "Actions", value: "actions" },
			],
			headersFinished: [
				{
					text: "Task",
					align: "start",
					sortable: true,
					value: "task",
				},
				{ text: "Priority", value: "priority" },
				{ text: "Note", value: "note" },
			],
			todos: [
				{
					task: "bernafas",
					priority: "Penting",
					note: "huffttt",
				},
				{
					task: "nongkrong",
					priority: "Tidak penting",
					note: "bersama tman2",
				},
				{
					task: "masak",
					priority: "Biasa",
					note: "masak air 500ml",
				},
			],
			finishedTodos: [],
			formTodo: {
				task: null,
				priority: null,
				note: null,
			},
		};
	},
	methods: {
		save() {
			if (this.editIndex > -1) this.todos.splice(this.editIndex, 1, this.formTodo);
			else this.todos.push(this.formTodo);

			this.resetForm();
			this.dialog = false;
			this.editIndex = -1;
		},
		cancel() {
			this.resetForm();
			this.dialog = false;
			this.editIndex = -1;
		},
		resetForm() {
			this.formTodo = {
				task: null,
				priority: null,
				note: null,
			};
		},
		editItem(item) {
			this.editIndex = this.todos.indexOf(item);

			this.formTodo = {
				task: item.task,
				priority: item.priority,
				note: item.note,
			};
			this.dialog = true;
		},
		deleteItem(item) {
			this.editIndex = this.todos.indexOf(item);
			this.dialogDelete = true;
		},
		cancelDelete() {
			this.dialogDelete = false;
			this.editIndex = -1;
		},
		confirmDelete() {
			this.finishedTodos.push(this.todos[this.editIndex]);
			this.todos.splice(this.editIndex, 1);
			this.dialogDelete = false;
			this.editIndex = -1;
		},
		getColor(prioritas) {
			if (prioritas === "Penting") return "red";
			else if (prioritas === "Biasa") return "blue";
			else return "green";
		},
	},
};
</script>
