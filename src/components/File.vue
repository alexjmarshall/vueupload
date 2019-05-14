<template>
    <div class="dragndrop__file">
        <div class="progress">
            <div class="progress__label">{{ file.file.name }} (x seconds remaining)</div>
            <div class="progress__fill" style="width: 60%"></div>
            <div class="progress__percentage">
                20%
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data () {

    },
    props: [
        'file'
    ]
}
</script>

<style>
    .dragndrop__file {
        margin: 20px;
        margin-top: 0;
        font-size: .9em;
        position: relative;
    }
    .progress {
        box-shadow: inset 0 1px 2px rgba(0, 0, 0, .1);
        border-radius: 3px;
        background-color: #f5f5f5;
        height: 35px;
    }
    .progress__label,
    .progress_percentage {
        position: absolute;
        color: #333;
        top: 50%;
        transform: translate(0, -50%);
        margin-left: 10px; 
    }
    .progress__percentage {
        right: 0;
        margin-right: 10px;
    }
    .progress__fill {
        box-sizing: border-box;
        padding: 10px;
        border-radius: 3px;
        background-color: #42b983;
        height: 100%;
        box-shadow: inset 0 -1px rgba(0, 0, 0, .15);
        transition: width 500ms ease;
        opacity: .6;
    }
    .progress__fill--failed {
        transition: none;
        width: 100%!important;
        background-color: #f66;
    }
    .progress__fill--finished {
        opacity: 1;
    }
</style>
