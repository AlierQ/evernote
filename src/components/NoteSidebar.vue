<template>
  <div class="note-sidebar">
    <span class="add-note" @click="addNote">
      <plus theme="filled" size="18" fill="#4a4a4a" :strokeWidth="3" />
      添加笔记
    </span>
    <el-dropdown
      trigger="click"
      class="notebook-title"
      @command="handleCommand"
    >
      <span class="el-dropdown-link">
        {{ useNotebooks.currentNotebook?.title || "暂无笔记本" }}
        <down theme="filled" size="20" fill="#4a4a4a" :strokeWidth="3" />
      </span>
      <template #dropdown>
        <el-dropdown-menu>
          <el-dropdown-item
            v-for="notebook in useNotebooks.notebooks"
            :key="notebook.id"
            :command="notebook"
          >
            <notebook-one
              theme="outline"
              size="16"
              fill="#606266"
              :strokeWidth="3"
            />&nbsp;{{ notebook.title }}</el-dropdown-item
          >
          <el-dropdown-item command="trash">
            <delete-one
              theme="outline"
              size="16"
              fill="#606266"
              :strokeWidth="3"
            />
            &nbsp;回收站
          </el-dropdown-item>
        </el-dropdown-menu>
      </template>
    </el-dropdown>
    <div class="menu">
      <div>标题</div>
      <div>更新时间</div>
    </div>
    <ul class="notes">
      <li v-for="note in useNotes.notes" :key="note.id">
        <router-link
          :class="{
            ['active']: useNotes.currentNote?.id === note.id,
          }"
          :to="`/note?noteId=${note.id}&notebookId=${useNotebooks.currentNotebook?.id}`"
          @click.prevent="selectedNote(note)"
        >
          <span class="title">{{ note.title }}</span>
          <span class="date">{{ formatDate(note.updatedAt) }}</span>
        </router-link>
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { Down, Plus, DeleteOne, NotebookOne } from "@icon-park/vue-next";
import { useRouter } from "vue-router";
import { formatDate } from "@/helpers/util";
import { useNotebooksStore } from "@/stores/notebook";
import { useNotesStore } from "@/stores/notes";
// pinia全局状态管理
const useNotebooks = useNotebooksStore();
const useNotes = useNotesStore();
// 初始化数据
useNotebooks.getNotebooks().then(() => {
  useNotebooks.getCurrentNotebook().then(() => {
    useNotes.getNotes();
  });
});

const router = useRouter();

const handleCommand = (command: string | number | object) => {
  if (command === "trash") {
    router.push("/trash");
  } else {
    useNotebooks.setCurrentNotebook(command);
    useNotes.getNotes();
    useNotes.setCurrentNote({ title: "", content: "" });
    router.push("/note?notebookId=" + useNotebooks.currentNotebook.id);
  }
};

const addNote = () => {
  useNotes.addNotes();
};

const selectedNote = (note: any) => {
  useNotes.setCurrentNote(note);
};
</script>

<style lang="less">
@import "@/assets/style/note-sidebar.less";
</style>
