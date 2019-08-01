<template>
    <div class="card rounded overflow-hidden shadow-lg">
        <div class="bg-white shadow-md rounded px-8 pt-6 pb-8">
            <div class="bg-blue-100 border border-blue-400 text-blue-700 px-4 py-3 rounded relative mb-3" role="alert">
                <span class="block sm:inline">短网址还原可以把压缩的网址还原成原来的网址，把真实的网址无所遁形。</span>
            </div>
            
            <div class="bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded relative mb-3" role="alert" v-if="long_url">
                <span class="block sm:inline">已还原：<a :href="long_url" target="_blank" rel="nofollow">{{ long_url  }}</a></span>
            </div>

            <div class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative mb-3" role="alert" v-if="message">
                <span class="block sm:inline">{{ message }}</span>
            </div>

            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="short_url">
                    短网址
                </label>
                <input @keypress.enter="submit()" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="short_url" type="text" placeholder="请输入要还原的短网址" v-model="short_url" required autofocus>
            </div>
            <div class="flex items-center justify-between">
                <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" type="button" id="submit-un" @click="submit()">
                    立即还原
                </button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                short_url: null,
                long_url: null,
                message: null,
                base_url: window.apiBaseUrl,
            }
        },

        mounted() {
        },

        methods: {
            submit() {
                this.message = this.long_url = null;

                if (!this.short_url) {
                    this.message = '请填写需要还原的短网址';
                    return;
                }

                window.nprogress.start();

                $('#submit-un').attr('disabled', 'disabled');
                $('#submit-un').addClass('opacity-50');
                $('#submit-un').html('还原中...');

                var _this = this;

                window.axios.get(this.base_url + 'api/un_short_url?url=' + encodeURIComponent(this.short_url))
                    .then(function (response) {
                        var data = response.data;

                        if (data.error !== '0') {
                            _this.message = data.error;
                        } else {
                            _this.long_url = data.url_long;
                        }

                        $('#submit-un').removeAttr('disabled');
                        $('#submit-un').removeClass('opacity-50');
                        $('#submit-un').html('立即还原');

                        window.nprogress.done();
                    })
                    .catch(function (error) {
                        _this.message = '请求后端接口失败';

                        $('#submit-un').removeAttr('disabled');
                        $('#submit-un').removeClass('opacity-50');
                        $('#submit-un').html('立即还原');

                        window.nprogress.done();
                    });
            }
        }
    }
</script>
