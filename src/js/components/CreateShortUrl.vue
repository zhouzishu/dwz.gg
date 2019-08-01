<template>
    <div class="card rounded overflow-hidden shadow-lg">
        <div class="bg-white shadow-md rounded px-8 pt-6 pb-8">
            <div class="bg-blue-100 border border-blue-400 text-blue-700 px-4 py-3 rounded relative mb-3" role="alert">
                <span class="block sm:inline">本站生成的短网址永久有效，请放心使用，永久网址：eps.gs。</span>
            </div>

            <div class="bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded relative mb-3" role="alert" v-if="short_url">
                <span class="block sm:inline">已生成：<a :href="short_url" target="_blank" rel="nofollow">{{ short_url  }}</a></span>
            </div>

            <div class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative mb-3" role="alert" v-if="message">
                <span class="block sm:inline">{{ message }}</span>
            </div>

            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="long_url">
                    长网址
                </label>
                <input @keypress.enter="submit()" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="long_url" type="text" placeholder="请输入您需要缩短的长网址" v-model="long_url" required autofocus>
            </div>
            <div class="mb-6 md:flex md:items-center ">
                <div class="md:w-1/5">
                    <label class="block text-gray-500 font-bold md:text-right mb-1 md:mb-0 pr-4" for="slug">
                        {{ base_url }}
                    </label>
                </div>
                <div class="md:w-4/5">
                    <input @keypress.enter="submit()" class="bg-gray-200 appearance-none border-2 border-gray-200 rounded w-full py-2 px-4 text-gray-700 leading-tight focus:outline-none focus:bg-white focus:border-purple-500" id="slug" type="text" placeholder="（可选，不填自动生成）" v-model="slug">
                </div>
            </div>
            <div class="flex items-center justify-between">
                <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" type="button" id="submit-create" @click="submit()">
                    立即生成
                </button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                long_url: null,
                short_url: null,
                message: null,
                slug: '',
                base_url: window.apiBaseUrl,
            }
        },

        mounted() {
        },

        methods: {
            submit() {
                this.message = this.short_url = null;

                if (!this.long_url) {
                    this.message = '请填写需要生成的短网址';
                    return;
                }

                window.nprogress.start();

                $('#submit-create').attr('disabled', 'disabled');
                $('#submit-create').addClass('opacity-50');
                $('#submit-create').html('生成中...');

                var _this = this;

                window.axios.get(this.base_url + 'api/create_short_url?url=' + encodeURIComponent(this.long_url) + '&slug=' + this.slug)
                    .then(function (response) {
                        var data = response.data;

                        if (data.error !== '0') {
                            _this.message = data.error;
                        } else {
                            _this.short_url = data.url_short;
                        }

                        $('#submit-create').removeAttr('disabled');
                        $('#submit-create').removeClass('opacity-50');
                        $('#submit-create').html('立即生成');

                        window.nprogress.done();
                    })
                    .catch(function (error) {
                        _this.message = '请求后端接口失败';

                        $('#submit-create').removeAttr('disabled');
                        $('#submit-create').removeClass('opacity-50');
                        $('#submit-create').html('立即生成');

                        window.nprogress.done();
                    });
            }
        }
    }
</script>
