<template>
    <div class="alert alert-dismissible alert-warning">
        <!--<button type="button" class="close" data-dismiss="alert">&times;</button>-->
        <strong>模块配置</strong>
        <div class="mokuaiConfig checkbox">
            <template v-for="(value, key) in MODARRAY">
                <label>
                    <input @change="updateMokuai"
                           :value="key"
                           v-if="Object.keys(mokuai).indexOf(key)!=-1"
                           checked
                           type="checkbox">
                    <input @change="updateMokuai"
                           :value="key"
                           v-else
                           type="checkbox"> {{value.name}}
                </label>
            </template>

        </div>
    </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex';
import { MODARRAY } from '../const';
import alert from './alert';
import Cookie from 'js-cookie';
import api from '../api';

export default {
    components: {
        alert
    },
    data: () => ({
        MODARRAY
    }),
    computed: {
        ...mapGetters([
            'mokuai',
        ]),
    },
    methods: {
        updateMokuai(e) {
            let _mokuai = Object.keys(this.mokuai);
            if (e.target.checked) {
                _mokuai.push(e.target.value);
            } else {
                let index = _mokuai.indexOf(e.target.value);
                if (index != -1) {
                    _mokuai.splice(index, 1);
                }
            }

            //写入用户数据库
            if (Cookie.get('usr_id')) {
                api.editMokuai((res) => { }, {
                    mokuai: _mokuai.join(','),
                    __CSRF__: Cookie.get('__CSRF__')
                });
            }
            //记录cookie
            Cookie.set('usr_mokuai', _mokuai.join(','), { expires: 366 });
            let mokuaiObj = {};
            for (let m of _mokuai) {
                mokuaiObj[m] = {};
            }
            this.$store.commit('updateParam', {
                key: ['mokuai'],
                value: [mokuaiObj]
            });
        },
    }
}
</script>