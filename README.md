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
