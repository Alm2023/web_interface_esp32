<script>
    
    import { onMount,onDestroy } from 'svelte';
    import { createEventDispatcher } from 'svelte';

const dispatch = createEventDispatcher();

export let myurl;


onMount(() => {
getstatus();
});

onDestroy(() => {
		clearInterval(interval);
	});

    export let lednum;
    let mystatus='';

    
    async function getstatus(){
    
    const interval = setInterval(async () => {
        const res = await fetch(myurl+`/status/`+lednum);
        let myst = await res.json();
        mystatus=myst.result;
        dispatch('message', { led:lednum,  statusresult: mystatus  });
        
        
    
        
	}, 2000);
    }
</script>

<div class ="{mystatus>0?'rcorners1':'rcorners2'}">{mystatus>0?'ON':'OFF'}</div>

<style>
    .rcorners1 {
  border-radius: 25px;
  color:  #73AD21;
  padding: 12px; 
  
}

.rcorners2 {
  border-radius: 25px;
  color: #a5a5a4;
  padding: 12px; 
  
}
    </style>