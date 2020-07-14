<template>
	<div id="app">
		<router-view/>
	</div>
</template>

<style>
.content {
    margin: 0 auto;
    max-width: 1180px;
}
h1, h2 {
    color: #0093d5;
    text-transform: uppercase;
    margin: 30px 0 25px;
    font-size: 30px;
    line-height: 36px;
}
.page-calc__archive {
    flex-wrap: wrap;
}
.page-calc {
    display: flex;
}
.page-params {
    display: flex;
    min-width: 100%;
    margin-bottom: 26px;
    border-top: 1px solid #0093d5;
    border-bottom: 1px solid #0093d5;
    position: relative;
    padding: 14px 18px;
    box-sizing: border-box;
}
.page-totals {
    min-width: 100%;
    display: flex;
    justify-content: space-between;
}
</style>
