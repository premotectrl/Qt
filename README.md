Commands:
dist forlder for Output after building:
D:\red-river-frontend\build\app\jspm\jspm_packages\npm\mobile-robotics-frontend@0.1.0\dist

Commands:
literServ  at D:\red-river-frontend.. bla bla:
lite-server -c litesrv.json
to install it: npm install lite-server -g

combined build command: 
tsc -p src/angular4/tsconfig.json && jspm build src/module.js dist/output.js --format cjs
--- new
tsc -p src/angular4/tsconfig.json && jspm build src/module.js dist/mobile-robotics.min.js --format cjs --minify

to copy  build files to redfront folder for preview:
robocopy D:\mobile-robotics-frontend\dist  D:\red-river-frontend\build\app\jspm\jspm_packages\npm\mobile-robotics-frontend@0.1.0\dist

/**************************************************************************/



link.....

Adhoc:
https://pdfs.semanticscholar.org/presentation/3e30/1bbb4c5300cf502fdc25bde347b5eca6ae81.pdf


https://bitbucket.org/kukaaustin/mobile-robotics-frontend/src/master/

https://css-tricks.com/fixing-tables-long-strings/

https://developer.mozilla.org/en-US/docs/Web/CSS/transform#matrix()

https://www.youtube.com/watch?v=PS96nkFfWcg


VUE.js

<script src = "https://unpkg.com/vue@2.5.16/dist/vue.js"> </script>
<div id ="root">
    <input type="text" id ="input" v-model ='msg'>
    <h1> {{msg}} </h1>
</div>
<script> 
    new Vue({
        el: '#root',
        msg: 'Now you see me'
    })
</script>

------- mobile-robotics- tab - component
 *ngIf="loaded | async"

 import {Component, OnInit, AfterViewInit, ChangeDetectorRef, OnDestroy} from "@angular/core";
import {FormsModule,ReactiveFormsModule} from '@angular/forms'
import {OperationalDataService} from "./services/operational-data.service";
import {DeviceManagementService} from "./services/device-management.service";
import {AGVStateCount, AGVStateCountGroup} from "./mobile-robotics-tab.component";
import {GaugeConfig} from "./components/gauge.component";
import { Subscription, Observable } from "rxjs";
import { AgvDataService } from "./services/agv-data.service";
import { KpiResult, KpiService } from "./services/kpi.service";

@Component({
    selector: 'mobile-robotics-card',
    template:
    `
        <div class="card-content">
            
            <div class="section-header" translate>views.section.map</div>
            <map-view></map-view>
            
            <hr class ="section-separator">
            
            <div class="row">
                <div class="col-xs-12 col-sm-12 col-md-12 col-lg-6">
                    <div class="section-header" translate>views.section.fleetData</div>
                    <div id="controls-wrapper">
                       <form class="radio">
                             <ng-container *ngFor="let stateCount of agvStateCountGroups;trackBy: trackByFn  index as i">
                                <input type="radio" name="agvCounts" [value]="i" (change)="changeChartView(i)" [id]="i" [checked]="stateCount == agvStateCountGroups[0]">
                                <label class="radio-label" [for]="i"> {{stateCount.title | translate}} </label>
                            </ng-container> 
                        </form>        
                    </div>

                    <div class="flexPos">
                        <scalar-chart *ngIf= "stateCountIndex == 0" [chartConfig]="commandabilityStateCount"></scalar-chart>
                        <scalar-chart *ngIf= "stateCountIndex == 1" [chartConfig]="connectionStateCount"></scalar-chart>
                        <scalar-chart *ngIf= "stateCountIndex == 2" [chartConfig]="poseReliabilityCount"></scalar-chart>
                    </div>
                </div>

                <div class="col-xs-12 col-sm-12 col-md-12 col-lg-6 vLine">
                    <hr class ="section-separator" id="hiddenHoriz">
                    <div class="section-header" translate>views.section.safetyStates</div>
                    <div class="grid-container">
                        <state-count [state]="safetyStateCount_safe"></state-count>
                        <state-count [state]="safetyStateCount_ack"></state-count>
                        <state-count [state]="safetyStateCount_warning"></state-count>
                        <state-count [state]="safetyStateCount_protective"></state-count>
                        <state-count [state]="safetyStateCount_eStop"></state-count>
                    </div>
                </div>
            </div>

            <hr class ="section-separator">
            
            <div class="section-header" translate>views.section.batteryLifeMean</div>

            <!-- Lower Row -------------->
            <div class="row">
                <div class="col-xs-12 col-sm-12 col-md-12 col-lg-6">
                    <div class="gauges">
                        <div class="flex-Container">
                            <gauge [gaugeConfig]="meanBatteryGaugeConfig" [gaugeLevel]="meanBatteryGaugeLevel"></gauge>
                        </div>
                    </div>
                </div>

            <div class="col-xs-12 col-sm-12 col-md-12 col-lg-6 vLine">
                <hr class ="section-separator" id="hiddenHoriz">
                <div class="section-header" translate>views.section.fleetData</div>
                <div id="controls-wrapper">
                   <form class="radio">
                         <ng-container *ngFor="let countGroup of  agvKpiGroups;trackBy: trackByFn  index as i">
                            <input type="radio" name="agvGroups" [value]="i" [id]="i" [checked]="countGroup == agvKpiGroups[0]">
                           <label class="radio-label" [for]="i"> {{countGroup.title | translate}} </label>
                        </ng-container> 
                    </form>        
                </div>

                <div class="flexPos">
                    <scalar-chart *ngIf= "stateCountIndex == 0" [chartConfig]=" connectionStateKpi"></scalar-chart>
                    <scalar-chart *ngIf= "stateCountIndex == 1" [chartConfig]="commandabilityKpi"></scalar-chart>
                    <scalar-chart *ngIf= "stateCountIndex == 2" [chartConfig]=" poseReliabilityKpi"></scalar-chart>
                </div>
        
            </div>
        </div>

    </div>
    `,  styles: [
        `
            .section-header {
                font-weight: bold;
                font-size: 1rem;
                font-family: kuka-bulo-bold;
                color: #676C71;
            }

            .card-content {
                position: static;
                display: block;
                padding: 15px;
            }

            .gauges {
                padding: 10px;
                padding-bottom: 50px;
            }

            .section-separator {
                width: 98%;
                color: #676c71;
                height:3px;
            }

            body hr {
                border-top: 1px solid #a4a7aa;
            }

            .flex-Container {
                display: flex;
                flex-direction:row;
                flex-wrap:wrap;
                justify-content: left;
                padding: 20px 30px 0 40px;
                float: left;
            }

            div.flexPos {
                display:flex;
                justify-content:space-around;
            }

            @media screen and (max-width:1200px){
                hr#hiddenHoriz {
                    display: block;
                }
                
                div.vLine {
                    border:none;
                    padding: 0 15px 0 15px;
                }
                
                div.grid-container {
                    justify-content: space-evenly;
                }

                div.flexPos {
                    display:flex;
                    justify-content:space-around;
                }
            }

            .grid-container {
                display: grid;
                grid-template-columns: repeat(auto-fill, 145pt);
                grid-row-gap: 1rem;
                grid-column-gap: 2rem;
                justify-content: left;
                list-style: none;
                padding: 1rem;
                width: 100%;
                margin: 0 auto;
            }
            
            #hiddenHoriz {
                display:none;
            }

            .vLine {
                border-left:1px solid#a4a7aa; /*#676c71;*/
                height:auto;
                padding-left: 30px;
            }

            form {
                z-index: 100;
                position: static;
                display: block;
            }
            
            #controls-wrapper {
                display: flex;
                flex-direction: row;
                justify-content: space-between;
                align-items: center;
                margin: auto;
                width: 98%;
            }
            
            .radio input[type="radio"] {
                position: absolute;
                opacity: 0;
            }
            
            .radio input[type="radio"] + .radio-label:before {
                content: '';
                background: #FFFFFF;
                border-radius: 100%;
                border: 2px solid #bfbfbf;
                display: inline-block;
                width: 1.2em;
                height: 1.2em;
                position: relative;
                margin-right: 0.8em;
                margin-bottom: 0.5em;
                padding-left: 0px;
                vertical-align: top;
                cursor: pointer;
                text-align: center;
                transition: all 250ms ease;
            }
            
            .radio input[type="radio"]:checked + .radio-label:before {
                background-color: #FF5800;
                box-shadow: inset 0 0 0 4px #FFFFFF;
                border-color: #FF5800;
            }
            
            .radio input[type="radio"]:checked:hover + .radio-label:before {
                outline: none;
                box-shadow: inset 0 0 0 4px #ffdecc;
            }
            
            .radio input[type="radio"]:hover + .radio-label:before {
                outline: none;
                border-color: #FF5800;
            }
            
            .radio input[type="radio"]:disabled + .radio-label:before {
                box-shadow: inset 0 0 0 4px #FFFFFF;
                border-color: #bfbfbf;
                background: #bfbfbf;
            }
            
            .radio input[type="radio"] + .radio-label:empty:before {
                margin-right: 0;
            }
        `
    ]
})
export class MobileRoboticsCardComponent implements OnInit, OnDestroy, AfterViewInit {
    private devicesMeta$: Observable<any[]>;
    private devicesMeta: any[];
    private deviceOperationalData = [];

    private commandabilityStateCount:   AGVStateCountGroup = null;
    private safetyStateCount:           AGVStateCountGroup = null;
    private poseReliabilityCount:       AGVStateCountGroup = null;
    private connectionStateCount:       AGVStateCountGroup = null;

    /**PETE */
    safetyStateKpi: AGVStateCountGroup = null;
    connectionStateKpi: AGVStateCountGroup = null;
    poseReliabilityKpi: AGVStateCountGroup = null;
    commandabilityKpi: AGVStateCountGroup = null;
    
    private default_safetyStateConf:    AGVStateCountGroup = null;
    private agvStateCountGroups : any = [];
    private agvKpiGroups : any = [];
    stateCountIndex :number = 0;
    private showAgvStateCount: any;

    private safetyStateCount_safe: AGVStateCount = null;
    private safetyStateCount_warning: AGVStateCount = null;
    private safetyStateCount_protective: AGVStateCount = null;
    private safetyStateCount_ack: AGVStateCount = null;
    private safetyStateCount_eStop: AGVStateCount = null;
   
    private meanBatteryGaugeConfig: GaugeConfig = null;
    private meanBatteryGaugeLevel: number = 0;
    
    public destroyable = true;

    private canDetectChanges: boolean = false;
    private subscriptions: Subscription[] = [];

    constructor(private deviceManagementService: DeviceManagementService,
                private operationalDataService: OperationalDataService,
                private kpiService: KpiService,
                private changeDetectorRef: ChangeDetectorRef,
                private agvDataService: AgvDataService) {

        this.meanBatteryGaugeConfig = {
            title: "AGV Fleet",
            defaultIcon: "assets/mobile-robotics/battery-unknown.svg",
            icons: ["./assets/KMP/battery-0-10.svg",
                "./assets/KMP/battery-11-25.svg",
                "assets/mobile-robotics/battery-26-50.svg",
                "assets/mobile-robotics/battery-51-75.svg",
                "assets/mobile-robotics/battery-76-100.svg",
                "assets/mobile-robotics/battery-unknown.svg"],
            colors: ["#CF2027","#FF5800","#FFCD00","#6EC8A0","#1B8642"],
            thresholds: [],
            showNeedle: false,
        };

        this.default_safetyStateConf = {
            title: "views.productivityInsights.secondaryKpis.MRSafetyState.title",
            showTitle: false,
            icon: "assets/mobile-robotics/safe-graph.svg",
            showCount: true,
            kpis: new Map()
                .set("mr-safety-state-SAFE",                    {id: 1,     title: "views.productivityInsights.secondaryKpis.MRSafetyState.safe",                   icon: "assets/mobile-robotics/safe-safe-gray.svg",       color: "#676c71", value: 0, samples: 0, count: 0})
                .set("mr-safety-state-ACKNOWLEDGE_REQUIRED",    {id: 4,     title: "views.productivityInsights.secondaryKpis.MRSafetyState.acknowledgeRequired",    icon: "assets/mobile-robotics/safe-ack-gray.svg",        color: "#676c71", value: 0, samples: 0, count: 0})
                .set("mr-safety-state-WARNING_FIELD",           {id: 2,     title: "views.productivityInsights.secondaryKpis.MRSafetyState.warningField",           icon: "assets/mobile-robotics/safe-warning-gray.svg",    color: "#676c71", value: 0, samples: 0, count: 0})
                .set("mr-safety-state-PROTECTIVE_STOP",         {id: 3,     title: "views.productivityInsights.secondaryKpis.MRSafetyState.protectiveStop",         icon: "assets/mobile-robotics/safe-protected-gray.svg",  color: "#676c71", value: 0, samples: 0, count: 0})
                .set("mr-safety-state-EMERGENCY_STOP",          {id: 5,     title: "views.productivityInsights.secondaryKpis.MRSafetyState.emergencyStop",          icon: "assets/mobile-robotics/safe-estop-gray.svg",      color: "#676c71", value: 0, samples: 0, count: 0})
        }; 

        
        this.default_safetyStateConf.kpis.forEach((value: AGVStateCount, key: string, map: Map<string, AGVStateCount>) => {
           
            let newSafetyStateCount: AGVStateCount = value;
         
            if (value.id == 1) this.safetyStateCount_safe = newSafetyStateCount;
            else if (value.id == 2) this.safetyStateCount_warning = newSafetyStateCount;
            else if (value.id == 3) this.safetyStateCount_protective = newSafetyStateCount;
            else if (value.id == 4) this.safetyStateCount_ack = newSafetyStateCount;
            else if (value.id == 5) this.safetyStateCount_eStop = newSafetyStateCount;
        });

        this.commandabilityStateCount = Object.assign({}, this.commandabilityStateConf);
        this.poseReliabilityCount = Object.assign({}, this.poseReliabilityStateConf);
        this.connectionStateCount = Object.assign({}, this.connectionStateConf);
        this.safetyStateCount = Object.assign({}, this.safetyStateConf);
        this.agvStateCountGroups = [this.commandabilityStateCount, this.connectionStateCount, this.poseReliabilityCount];

        this.meanBatteryGaugeLevel = 0; 
        this.meanBatteryGaugeConfig = Object.assign({}, this.meanBatteryGaugeConfig);

        /************************ KPI ******************/
        this.safetyStateKpi = Object.assign({}, this.safetyStateKpiConf);
        this.connectionStateKpi = Object.assign({}, this.connectionStateKpiConf);
        this.poseReliabilityKpi = Object.assign({}, this.poseReliabilityKpiConf);
        this.commandabilityKpi = Object.assign({}, this.commandabilityKpiConf);
        this.agvKpiGroups = [this.connectionStateKpi, this.safetyStateKpi, this.poseReliabilityKpi, this.commandabilityKpi];
    }

    public ngOnInit() {
        this.canDetectChanges = true;
        //this.showAgvStateCount = this.commandabilityStateCount;
    }

    private changeChartView(stateIndex: number){
       
        switch(stateIndex){
            case 0:{
            this.stateCountIndex = stateIndex;
            this.commandabilityStateCount =  Object.assign({},this.commandabilityStateCount);
            break;
            }
            case 1: {
            this.stateCountIndex = stateIndex;
            this.connectionStateCount =  Object.assign({},this.connectionStateCount);
            break;
            }
            case 2: {
            this.stateCountIndex = stateIndex;
            this.poseReliabilityCount = Object.assign({}, this.poseReliabilityCount);
            break;
            }
            default:{
            //this.showAgvStateCount = this.commandabilityStateCount;
            break;}
        }
        
    }

    ngAfterViewInit(){
    let sub = this.agvDataService.getAgvsFromScope()
        .subscribe(agvs => {
            this.deviceOperationalData = agvs;
            let test = this.commandabilityStateCount;
            this.update(this.commandabilityStateConf,   this.getCommandabilityStates(agvs),     state => this.commandabilityStateCount = state);
            this.update(this.poseReliabilityStateConf,  this.getPoseReliabilityStates(agvs),    state => this.poseReliabilityCount = state);
            this.update(this.connectionStateConf,       this.getConnectionStates(agvs),         state => this.connectionStateCount = state);
            this.update(this.safetyStateConf,           this.getSafetyStates(agvs),             state => this.safetyStateCount = state);
         
            this.updateSafetyStateCount(agvs);
            this.meanBatteryGaugeLevel =  this.getMeanBatterySoc(agvs);
            this.changeChartView(this.stateCountIndex);

            if (this.canDetectChanges) {
                this.changeDetectorRef.detectChanges();
            }
        });
    this.subscriptions.push(sub);
    this.updateKPIs();
    }

    public ngOnDestroy() {
        for (let sub of this.subscriptions) {
            sub.unsubscribe();
        }
    }

    private getMeanBatterySoc(vals: any[]): number {
        const sum = vals
            .filter(op => op.mobilityState && op.mobilityState.battery && op.mobilityState.battery.batterySoc)
            .reduce((acc, val) =>  acc + val.mobilityState.battery.batterySoc, 0);

        return vals.length ? 100 * sum / vals.length : 0.0;
    }

    private getConnectionStates(vals: any[]): Array<number> {
        return vals
            .filter(op => op.mobilityState && op.mobilityState.vmsMobileRobotState && op.mobilityState.vmsMobileRobotState.connectionState)
            .map(op => {
                return op.mobilityState.vmsMobileRobotState.connectionState
            });
    }

    private getCommandabilityStates(vals: any[]): Array<number> {
        return vals
            .filter(op => op.mobilityState && op.mobilityState.vmsMobileRobotState && op.mobilityState.vmsMobileRobotState.commandableState)
            .map(op => {
                return op.mobilityState.vmsMobileRobotState.commandableState
            });
    }

    private getPoseReliabilityStates(vals: any[]): Array<number> {
        return vals
            .filter(op => op.mobilityState && op.mobilityState.mapLocalization && op.mobilityState.mapLocalization.poseReliabilitySimple)
            .map(op => {
                return op.mobilityState.mapLocalization.poseReliabilitySimple
            });
    }

    private getSafetyStates(vals: any[]): Array<number> {
        return vals
            .filter(op => op.mobilityState && op.mobilityState.safety && op.mobilityState.safety.safetyState)
            .map(op => {
                return op.mobilityState.safety.safetyState
            });
    }

    private countOccurences(values: number[], target:  number): number {
        let count = 0; // newStates.filter(val => val == ID).length;
        for (let newState of values) {
            if (newState == target)
                count++;
        }
        return count;
    }
   
    private updateSafetyStateCount(values: any[]) {
        const newSafetyStates: number[] = this.getSafetyStates(values);
        let safetyStateConfiguration = Object.assign({}, this.safetyStateConf);
        safetyStateConfiguration.kpis.forEach((value: AGVStateCount, key: string, map: Map<string, AGVStateCount>) => {
            const numberOfAgvsInThisState: number = this.countOccurences(newSafetyStates, value.id as number);
            let newSafetyStateCount: AGVStateCount = Object.assign({}, value);
            newSafetyStateCount.count = numberOfAgvsInThisState;

            if (value.id == 1) this.safetyStateCount_safe = newSafetyStateCount;
            else if (value.id == 2) this.safetyStateCount_warning = newSafetyStateCount;
            else if (value.id == 3) this.safetyStateCount_protective = newSafetyStateCount;
            else if (value.id == 4) this.safetyStateCount_ack = newSafetyStateCount;
            else if (value.id == 5) this.safetyStateCount_eStop = newSafetyStateCount;
        });
    }

    private update(stateConfiguration: AGVStateCountGroup, newStates: number[], updtFunc) {
        let stateGroup: AGVStateCountGroup = Object.assign({}, stateConfiguration);
        let newKpis: Map<string, AGVStateCount> = new Map();
        const AGV_COUNT = newStates.length;

        stateGroup.kpis.forEach((value, key, map) => {
            const ID: number = value.id as number;
            let COUNT = this.countOccurences(newStates, ID);
            const VALUE = COUNT * 100.0 / AGV_COUNT;

            let stateCount: AGVStateCount = Object.assign({}, value);
            stateCount.count = COUNT;
            stateCount.value = VALUE;
            newKpis.set(key, stateCount);
        });

        stateGroup.kpis = newKpis;
        updtFunc(stateGroup);
    }

    trackByFn(index, stateCount) {
        return index; // or item.id
      }
    // ====================================================
    // Config
    // ====================================================

    commandabilityStateConf: AGVStateCountGroup = {
        title: "views.productivityInsights.secondaryKpis.MRCommandability.title",
        showTitle: false,
        icon: "assets/mobile-robotics/command-graph.svg",
        showCount: true,
        kpis: new Map<string, AGVStateCount>()
                .set("mr-commandability-COMMANDABLE",                   {id: 1, title: "views.productivityInsights.secondaryKpis.MRCommandability.commandable",                 icon: "assets/mobile-robotics/command-ready.svg",   color: "#6EC8A0", value: 0, count: 0})
                .set("mr-commandability-TEMPORARILY_NOT_COMMANDABLE",   {id: 2, title: "views.productivityInsights.secondaryKpis.MRCommandability.temporarilyNotCommandable",   icon: "assets/mobile-robotics/command-active.svg",  color: "#1B8642", value: 0, count: 0})
                .set("mr-commandability-NOT_COMMANDABLE",               {id: 3, title: "views.productivityInsights.secondaryKpis.MRCommandability.notCommandable",              icon: "assets/mobile-robotics/command-not.svg",     color: "#CF2027", value: 0, count: 0})
        };

    safetyStateConf: AGVStateCountGroup = {
        title: "views.productivityInsights.secondaryKpis.MRSafetyState.title",
        showTitle: false,
        icon: "assets/mobile-robotics/safe-graph.svg",
        showCount: true,
        kpis: new Map()
            .set("mr-safety-state-SAFE",                    {id: 1,     title: "views.productivityInsights.secondaryKpis.MRSafetyState.safe",                   icon: "assets/mobile-robotics/safe-safe.svg",       color: "#1B8642", value: 0, samples: 0})
            .set("mr-safety-state-ACKNOWLEDGE_REQUIRED",    {id: 4,     title: "views.productivityInsights.secondaryKpis.MRSafetyState.acknowledgeRequired",    icon: "assets/mobile-robotics/safe-ack.svg",        color: "#9F547D", value: 0, samples: 0})
            .set("mr-safety-state-WARNING_FIELD",           {id: 2,     title: "views.productivityInsights.secondaryKpis.MRSafetyState.warningField",           icon: "assets/mobile-robotics/safe-warning.svg",    color: "#FFCD00", value: 0, samples: 0})
            .set("mr-safety-state-PROTECTIVE_STOP",         {id: 3,     title: "views.productivityInsights.secondaryKpis.MRSafetyState.protectiveStop",         icon: "assets/mobile-robotics/safe-protected.svg",  color: "#FF5800", value: 0, samples: 0})
            .set("mr-safety-state-EMERGENCY_STOP",          {id: 5,     title: "views.productivityInsights.secondaryKpis.MRSafetyState.emergencyStop",          icon: "assets/mobile-robotics/safe-estop.svg",      color: "#CF2027", value: 0, samples: 0})
    };// ['//#FF5800', '#316D92', '#C2D8EA', '#676C71', '#A2A5A8', '#314D7E', '#000000']

    connectionStateConf: AGVStateCountGroup = {
        title: "views.productivityInsights.secondaryKpis.MRConnection.title",
        showTitle: false,
        icon: "assets/mobile-robotics/connect-graph.svg",
        showCount: true,
        kpis: new Map()
            .set("mr-connection-CONNECTED",     {id: 1, title: "views.productivityInsights.secondaryKpis.MRConnection.connected",   icon: "assets/mobile-robotics/connect-yes.svg",         color: "#1B8642", value: 0, samples: 0})
            .set("mr-connection-UNRELIABLE",    {id: 2, title: "views.productivityInsights.secondaryKpis.MRConnection.unreliable",  icon: "assets/mobile-robotics/connect-unreliable.svg",  color: "#FFCD00", value: 0, samples: 0})
            .set("mr-connection-DISCONNECTED",  {id: 3, title: "views.productivityInsights.secondaryKpis.MRConnection.disconnected",icon: "assets/mobile-robotics/connect-not.svg",         color: "#CF2027", value: 0, samples: 0})
            .set("mr-connection-DISPOSED",      {id: 4, title: "views.productivityInsights.secondaryKpis.MRConnection.disposed",    icon: "assets/mobile-robotics/connect-disposed.svg",    color: "#BABDBF", value: 0, samples: 0})
        // .set("mr-connection-UNKNOWN",        {id: 5, title: "views.productivityInsights.secondaryKpis.MRConnection.unknown",     icon: "assets/mobile-robotics/connect-", color: "color"}
    };

    poseReliabilityStateConf: AGVStateCountGroup = {
        title: "views.productivityInsights.secondaryKpis.MRPoseReliability.title",
        showTitle: false,
        icon: "assets/mobile-robotics/pose-graph.svg",
        showCount: true,
        kpis: new Map()
            .set("mr-pose-reliability-RELIABLE",    {id: 1, title: "views.productivityInsights.secondaryKpis.MRPoseReliability.reliable",   icon: "assets/mobile-robotics/pose-yes.svg",        color: "#1B8642", value: 0, samples: 0})
            .set("mr-pose-reliability-UNRELIABLE",  {id: 2, title: "views.productivityInsights.secondaryKpis.MRPoseReliability.undecided",  icon: "assets/mobile-robotics/pose-undecided.svg",  color: "#BABDBF", value: 0, samples: 0})
            .set("mr-pose-reliability-UNDECIDED",   {id: 3, title: "views.productivityInsights.secondaryKpis.MRPoseReliability.unreliable", icon: "assets/mobile-robotics/pose-no.svg",         color: "#FFCD00", value: 0, samples: 0})
    };

    /************************************* KPI ***********/
    private updateKPIs() {
     
        let endTime: Date = new Date(Date.now());
        let startTime: Date = new Date(Date.now());
        startTime.setDate(endTime.getDate() - 7);

        this.pollKpi(this.safetyStateKpiConf,       startTime, endTime);
        this.pollKpi(this.connectionStateKpiConf,   startTime, endTime);
        this.pollKpi(this.poseReliabilityKpiConf,   startTime, endTime);
        this.pollKpi(this.commandabilityKpiConf,    startTime, endTime);
    }
    private triggerRefresh (state: AGVStateCountGroup) {
     
        if (state.title === "views.productivityInsights.secondaryKpis.MRSafetyState.title") {
            this.safetyStateKpi = state;
        }
        else if (state.title === "views.productivityInsights.secondaryKpis.MRConnection.title") {
            this.connectionStateKpi = state;
        }
        else if (state.title === "views.productivityInsights.secondaryKpis.MRPoseReliability.title") {
            this.poseReliabilityKpi =state;
        }
        else if (state.title === "views.productivityInsights.secondaryKpis.MRCommandability.title") {
            this.commandabilityKpi =state;
        }
    }

    private pollKpi(config, from: Date, to: Date) {
        let tmp: AGVStateCountGroup =  Object.assign({},  config);
        const sub = this.kpiService.getForAllMobileRobotsInScope(Array.from(config.kpis).map(([k, aa]) => aa.id), from, to)
            .subscribe((result: KpiResult[]) => {
        
                tmp.kpis.forEach(kpiConf => {
                    //let _kpiConf =  kpiConf;
                    result.forEach(res => {
                        if (res.name == kpiConf.id) {
                            if(res.value !== undefined){
                            kpiConf.value = res.value;
                            kpiConf.count = parseInt(res.samples);
                            }else{
                                kpiConf.value = 0;
                                kpiConf.count = parseInt(res.samples);
                            }
                        }
                        return res;
                    });
               
                    return kpiConf;
                });
                
                this.triggerRefresh(tmp);
                
            if (this.canDetectChanges) {
                this.changeDetectorRef.detectChanges();
            }

        });
      
        this.subscriptions.push(sub);
    }

    safetyStateKpiConf: AGVStateCountGroup = {
        title: "views.productivityInsights.secondaryKpis.MRSafetyState.title",
        showTitle: true,
        icon: "assets/mobile-robotics/safe-graph.svg",
        showCount: false,
        kpis: new Map()
            .set("mr-safety-state-SAFE",                    {id: "mr-safety-state-SAFE",                title: "views.productivityInsights.secondaryKpis.MRSafetyState.safe",                   icon: "assets/mobile-robotics/safe-safe.svg",       color: "#1B8642", value: 0, samples: 0})
            .set("mr-safety-state-ACKNOWLEDGE_REQUIRED",    {id: "mr-safety-state-ACKNOWLEDGE_REQUIRED",title: "views.productivityInsights.secondaryKpis.MRSafetyState.acknowledgeRequired",    icon: "assets/mobile-robotics/safe-ack.svg",        color: "#9F547D", value: 0, samples: 0})
            .set("mr-safety-state-WARNING_FIELD",           {id: "mr-safety-state-WARNING_FIELD",       title: "views.productivityInsights.secondaryKpis.MRSafetyState.warningField",           icon: "assets/mobile-robotics/safe-warning.svg",    color: "#FFCD00", value: 0, samples: 0})
            .set("mr-safety-state-PROTECTIVE_STOP",         {id: "mr-safety-state-PROTECTIVE_STOP",     title: "views.productivityInsights.secondaryKpis.MRSafetyState.protectiveStop",         icon: "assets/mobile-robotics/safe-protected.svg",  color: "#FF5800", value: 0, samples: 0})
            .set("mr-safety-state-EMERGENCY_STOP",          {id: "mr-safety-state-EMERGENCY_STOP",      title: "views.productivityInsights.secondaryKpis.MRSafetyState.emergencyStop",          icon: "assets/mobile-robotics/safe-estop.svg",      color: "#CF2027", value: 0, samples: 0})
    };

    connectionStateKpiConf: AGVStateCountGroup = {
        title: "views.productivityInsights.secondaryKpis.MRConnection.title",
        showTitle: true,
        icon: "assets/mobile-robotics/connect-graph.svg",
        showCount: false,
        kpis: new Map()
            .set("mr-connection-CONNECTED",     {id: "mr-connection-CONNECTED",     title: "views.productivityInsights.secondaryKpis.MRConnection.connected",   icon: "assets/mobile-robotics/connect-yes.svg",         color: "#1B8642", value: 0, samples: 0})
            .set("mr-connection-UNRELIABLE",    {id: "mr-connection-UNRELIABLE",    title: "views.productivityInsights.secondaryKpis.MRConnection.unreliable",  icon: "assets/mobile-robotics/connect-unreliable.svg",  color: "#FFCD00", value: 0, samples: 0})
            .set("mr-connection-DISCONNECTED",  {id: "mr-connection-DISCONNECTED",  title: "views.productivityInsights.secondaryKpis.MRConnection.disconnected",icon: "assets/mobile-robotics/connect-not.svg",         color: "#CF2027", value: 0, samples: 0})
            .set("mr-connection-DISPOSED",      {id: "mr-connection-DISPOSED",      title: "views.productivityInsights.secondaryKpis.MRConnection.disposed",    icon: "assets/mobile-robotics/connect-disposed.svg",    color: "#BABDBF", value: 0, samples: 0})
    };

    poseReliabilityKpiConf: AGVStateCountGroup = {
        title: "views.productivityInsights.secondaryKpis.MRPoseReliability.title",
        showTitle: true,
        icon: "assets/mobile-robotics/pose-graph.svg",
        showCount: false,
        kpis: new Map()
            .set("mr-pose-reliability-RELIABLE",    {id: "mr-pose-reliability-RELIABLE", title: "views.productivityInsights.secondaryKpis.MRPoseReliability.reliable",      icon: "assets/mobile-robotics/pose-yes.svg",        color: "#1B8642", value: 0, samples: 0})
            .set("mr-pose-reliability-UNRELIABLE",  {id: "mr-pose-reliability-UNRELIABLE", title: "views.productivityInsights.secondaryKpis.MRPoseReliability.undecided",   icon: "assets/mobile-robotics/pose-undecided.svg",  color: "#BABDBF", value: 0, samples: 0})
            .set("mr-pose-reliability-UNDECIDED",   {id: "mr-pose-reliability-UNDECIDED", title: "views.productivityInsights.secondaryKpis.MRPoseReliability.unreliable",   icon: "assets/mobile-robotics/pose-no.svg",         color: "#FFCD00", value: 0, samples: 0})
    };

    commandabilityKpiConf: AGVStateCountGroup = {
        title: "views.productivityInsights.secondaryKpis.MRCommandability.title",
        showTitle: true,
        icon: "assets/mobile-robotics/command-graph.svg",
        showCount: false,
        kpis: new Map()
            .set("mr-commandability-COMMANDABLE",                   {id: "mr-commandability-COMMANDABLE",                   title: "views.productivityInsights.secondaryKpis.MRCommandability.commandable",                 icon: "assets/mobile-robotics/command-ready.svg",   color: "#6EC8A0", value: 0, count: 0})
            .set("mr-commandability-TEMPORARILY_NOT_COMMANDABLE",   {id: "mr-commandability-TEMPORARILY_NOT_COMMANDABLE",   title: "views.productivityInsights.secondaryKpis.MRCommandability.temporarilyNotCommandable",   icon: "assets/mobile-robotics/command-active.svg",   color: "#1B8642", value: 0, count: 0})
            .set("mr-commandability-NOT_COMMANDABLE",               {id: "mr-commandability-NOT_COMMANDABLE",               title: "views.productivityInsights.secondaryKpis.MRCommandability.notCommandable",              icon: "assets/mobile-robotics/command-not.svg",     color: "#CF2027", value: 0, count: 0})
    };
}

