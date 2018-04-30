
    .flexContainer{
        display: grid;
        grid-template-columns: repeat(auto-fill, 140pt);
        /*grid-gap: 3rem;*/
        grid-row-gap: 2rem;
        grid-column-gap: 3rem;
        justify-content: space-evenly;
    
        /* boring properties */
        list-style: none;
       
        padding: 1rem;
        width: 100%;
        margin: 0 auto;
       
    }
    ul {
        display: grid;
        grid-template-columns: repeat(auto-fill, 145pt);
        grid-gap: 5rem;
        justify-content: space-evenly;
    
        /* boring properties */
        list-style: none;
        background: wheat;
        padding: 1rem;
        width: 100%;
        margin: 0 auto;
    }



style
  .state{
        width:90%;
        margin: 10px;
        display: inline-block;
       
        
    }
    .kco{
        padding: 5px;
    }
    
    HtML
    <div class="row">
		<div class="col-xs-4 col-sm-4 col-md-3 col-lg-3 kco">
		<div class ="state">	
			<app-safety-states [state]='safeState'></app-safety-states>
		</div>
		</div>
			<div class="col-xs-4 col-sm-4 col-md-3 col-lg-3 kco">
					<div class ="state">
			<app-safety-states [state]='ackState'></app-safety-states>
		</div>
		</div>
			<div class=" col-xs-4 col-sm-4 col-md-3 col-lg-3 kco">
					<div class ="state">
			<app-safety-states [state]='warningState'></app-safety-states>
		</div>
		</div>
			<div class=" col-xs-4 col-sm-4 col-md-3 col-lg-3 kco">
					<div class ="state">
			<app-safety-states [state]='protectState'></app-safety-states>
		</div>
		</div>
			<div class="col-xs-4 col-sm-4 col-md-3 col-lg-3 kco">
					<div class ="state">
			<app-safety-states [state]='errorState'></app-safety-states>
		</div>
		</div>
		
		
	</div>
	
	 #vSeparatorX div:nth-child(2n){
    border-left:1px solid gray;
  }

  .fujitsu-board:nth-child(even) {
    border-left: 1px solid #e1e2e3;
    padding-left: 3%;
    padding-bottom: 1%;
    height: auto; /* 210px **/
  
    
    }
    .fujitsu-board:nth-child(1) {
       text-align: center; 
    }
    .secondary-kpis-row{
        margin: 15px 0;
        display: table;
        width:100%;
    }
   
    .section-separator {
        width: 98%;
        color: #676c71;
        height: 3px;
    }

    .tiled{
        width:240px; 
        display: inline-block;
        float: none;
        
    }
    .Kcontainer{
        width:auto;  
    }
    .Kcontainer .tiled:nth-child(2n){
        margin-left: 20px;  
    }
    
link.....
https://css-tricks.com/fixing-tables-long-strings/
