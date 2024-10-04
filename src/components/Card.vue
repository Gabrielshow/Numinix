<template>
    <div class="card">
        <img :name={{avatar}} />
        <h2> {{ name }}</h2>
        <h4> {{ username }}</h2>
            <span> {{ about }}</span>
            <div>
                <li> <Icon src="mail"/> {{email}}</li>
                <li><Icon src="location"/> {{location}}</li>
                <li> <Icon src="call"/> {{ phone }}</Icon></li>
                <li> <Icon src="web"/> {{ website }}</li>
                <li><Icon src="briefcase"/>{{ work }}</li>
                <li><Icon src="fattory"> {{
                    workplace}} </li>
            </div>
</template>
