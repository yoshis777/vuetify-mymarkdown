<template>
    <div class="editor">
        <h1>エディター画面</h1>
        <span>{{ user.displayName }}</span>
        <button @click="logout">ログアウト</button>
        <div class="editorWrapper">
            <div class="memoListWrapper">
                <!-- :key=indexにより配列要素の識別子として:keyを与えることでパフォーマンス向上する。(DBでいうインデックスみたいな感じ) -->
                <!-- keyはユニークな識別子(memo.idみたいな) -->
                <div class="memoList" v-for="(memo, index) in memos" :key="index" @click="selectMemo(index)" :data-selected="index === selectedIndex">
                    <p class="memoTitle">{{ displayTitle(memo.markdown) }}</p>
                </div>
                <button class="addMemoBtn" @click="addMemo">メモの追加</button>
                <button class="deleteMemoBtn" @click="deleteMemo" v-if="memos.length > 1">選択中のメモを削除</button>
                <button class="saveMemosBtn" @click="saveMemos">メモの保存</button>
            </div>
            <textarea class="markdown" v-model="memos[selectedIndex].markdown" title=""></textarea>
            <div class="preview markdown-body" v-html="preview()"></div>
        </div>
    </div>
</template>