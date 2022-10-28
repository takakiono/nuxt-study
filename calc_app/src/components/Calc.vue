<template>
  <div class="hello">
    <h1>{{ title }}</h1>
    <p>{{ message }}</P>
    <hr>
    <div>
      <div>
        <textarea v-model="fomula" cols="40" rows="5"></textarea>
      </div>
      <div>
        <button v-on:click="doAction">CALC</button>
      </div>    
    </div>
  </div>
</template>

<script>
export default {
  name: 'AppCalc',
  props: {
    title: String,
  },
  data:function(){
    return {
      message: 'Enter expression:',
      fomula: '0',
    };
  },
  methods:{
    doAction: function(){
      // fomulaのテキストを改行コードで分割し配列にする
      var arr = this.fomula.trim().split('\n');

      // 配列の最後の項目を変数lastに取り出しておく（配列からは消える）
      var last = arr.pop();

      var fn = '';
      // 配列の各要素について繰り返し処理をする
      for(var n in arr){
        
        // 繰り返しでは、取り出した項目のテキストが空でないなら、「var テキスト;」というテキストを作って変数fnに追加する
        if(arr[n].trim() != ''){
          fn += 'var ' + arr[n] + ';';
        }
      }
      // 繰り返しが終わったら、「return 最後の項目;」とテキストを追加してテキスト完成
      fn += 'return ' + last + ';';

      // 完成したテキストの前後に「function(){」「} f();」と付け足す。これで、取り出した処理のテキストをｆという関数にまとめて実行するスクリプトが出来上がる
      var exp = 'function f(){' + fn + '} f();';

      // eval関数を使ってスクリプトのテキストを実行する
      var ans = eval(exp);

      // 実行結果をメッセージにまとめてmessageに設定
      this.message = 'answer: ' + ans;

      // 配列をセミコロンで繋げて１つのテキストにする
      var re = arr.join(';').trim();

      // 配列がなければセミコロンをつけ、最後の項目を付け加える
      if (re != ''){
        re += ';'
      }
      re += last;

      // 作成したテキストを引数に指定し、$emitでresult-eventイベントを呼び出す
      this.$emit('result-event', re, ans);
    }
  }
}
</script>