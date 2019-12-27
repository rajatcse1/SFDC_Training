# Tutorials
1. ## Template
    1. #### data binding in Template
    ```html
    <!-- hello.html -->
    <template>
        Hello, {greeting}!
    </template>
    ```
    ```js
    // hello.js
    import { LightningElement } from 'lwc';

    export default class Hello extends LightningElement {
        greeting = 'World';
    }
    ```
    --------------
    ```html
    <!-- helloBinding.html -->
    <template>
        <p>Hello, {greeting}!</p>
        <lightning-input label="Name" value={greeting} onchange={handleChange}></lightning-input>
    </template>
    ```
    ```js
    // helloBinding.js
    import { LightningElement, track } from 'lwc';

    export default class HelloBinding extends LightningElement {
        @track greeting = 'World';

        handleChange(event) {
            this.greeting = event.target.value;
        }
    }
    ```
    ---------------------
    ```html
    <!-- helloExpressions.html -->
    <template>
        <div class="slds-m-around_medium">
            <lightning-input name='firstName' label="First Name" onchange={handleChange}></lightning-input>
            <lightning-input name='lastName' label="Last Name" onchange={handleChange}></lightning-input>
            <p class="slds-m-top_medium">Uppercased Full Name: {uppercasedFullName}</p>
        </div>
    </template>
    ```
    ```js
    // helloExpressions.js
    import { LightningElement, track } from 'lwc';

    export default class HelloExpressions extends LightningElement {
        @track firstName = '';
        @track lastName = '';

        handleChange(event) {
            const field = event.target.name;
            if (field === 'firstName') {
                this.firstName = event.target.value;
            } else if (field === 'lastName') {
                this.lastName = event.target.value;
            }
        }

        get uppercasedFullName() {
            return `${this.firstName} ${this.lastName}`.toUpperCase();
        }
    }
    ```
    2. #### Render DOM Elements Conditionally
    ```html
    <!-- helloConditionalRendering.html -->
    <template>
        <lightning-card title="HelloConditionalRendering" icon-name="custom:custom14">
            <div class="slds-m-around_medium">
                <lightning-input type="checkbox" label="Show details" onchange={handleChange}></lightning-input>
                <template if:true={areDetailsVisible}>
                    <div class="slds-m-vertical_medium">
                        These are the details!
                    </div>
                </template>
            </div>
        </lightning-card>
    </template>
    ```
    ```js
    // helloConditionalRendering.js
    import { LightningElement, track } from 'lwc';

    export default class HelloConditionalRendering extends LightningElement {
        @track areDetailsVisible = false;

        handleChange(event) {
            this.areDetailsVisible = event.target.checked;
        }
    }
    ```
    3. #### Render Lists
    ```html
    <!-- helloForEach.html -->
    <template>
        <lightning-card title="HelloForEach" icon-name="custom:custom14">
            <ul class="slds-m-around_medium">
                <template for:each={contacts} for:item="contact">
                    <li key={contact.Id}>
                        {contact.Name}, {contact.Title}
                    </li>
                </template>
            </ul>
        </lightning-card>
    </template>
    ```
    ```js
    // helloForEach.js
    import { LightningElement, track } from 'lwc';

    export default class HelloForEach extends LightningElement {
        contacts = [
            {
                Id: 1,
                Name: 'Amy Taylor',
                Title: 'VP of Engineering',
            },
            {
                Id: 2,
                Name: 'Michael Jones',
                Title: 'VP of Sales',
            },
            {
                Id: 3,
                Name: 'Jennifer Wu',
                Title: 'CEO',
            },
        ];
    }
    ```
    ------------------------------
    ```html
    <template>
        <lightning-card title="HelloIterator" icon-name="custom:custom14">
            <ul class="slds-m-around_medium">
                <template iterator:it={contacts}>
                    <li key={it.value.Id}>
                        <div if:true={it.first} class="list-first"></div>
                        {it.value.Name}, {it.value.Title}
                        <div if:true={it.last} class="list-last"></div>
                    </li>
                </template>
            </ul>
        </lightning-card>
    </template>
    ```
    ```css
    .list-first {
        border-top: 1px solid black;
        padding-top: 5px;
    }

    .list-last {
        border-bottom: 1px solid black;
        padding-bottom: 5px;
    }
    ```
    4. #### Render Multiple Templates
    ```js
    // MultipleTemplates.js

    import { LightningElement, track  } from 'lwc';
    import templateOne from './templateOne.html';
    import templateTwo from './templateTwo.html';

    export default class MultipleTemplates extends LightningElement {

        @track templateOne = true;

        render() {
            return this.templateOne ? templateOne : templateTwo;
        }

        switchTemplate(){ 
            this.templateOne = this.templateOne === true ? false : true; 
        }
    }
    ```
    ```html
    <!-- templateOne.html -->
    <template>
        <lightning-card title="Template One">
            <div>
                This is template one.
            </div>
            <p class="margin-vertical-small">
                <lightning-button label="Switch Templates" 
                    onclick={switchTemplate}>
                </lightning-button> 
            </p>
        </lightning-card>
    </template>
    ```
    ```html
    <!-- templateTwo.html -->
    <template>
        <lightning-card title="Template Two">
            <div>
                This is template two.
            </div>
            <p class="margin-vertical-small">
                <lightning-button label="Switch Templates" 
                    onclick={switchTemplate}>
                </lightning-button> 
            </p>
        </lightning-card>
    </template>
    ```
2. ## CSS
    1. #### Lightning style
    ```html
    <template>
        <div class="slds-tree_container" role="application">
            <h4 class="slds-text-title_caps" id="treeheading">{header}</h4>
            <template if:true={state.node}>
                <lightning-tree-item class="slds-tree" role="tree" aria-labelledby="treeheading" node={state.node} is-root>
                </lightning-tree-item>
            </template>
        </div>
    </template>
    ```
    2. #### Aura style
    ```css
    /* myWebComponent.css */
    div {
        margin-right: var(--lwc-spacingSmall);
    }
    ```
    3. #### Basic CSS way
    ```html
    <!-- child.html -->
    <template>
    <h1>To Do Item</h1>
    <slot></slot>
    </template>
    ```
    ```css
    /* child.css */
    h1 {
        font-size: large;
    }
    :host {
        display: block;
        background: lightgray;
    }

    :host(.active) {
        background-color: lightgreen;
    } 
    ``` 
    ```html
    <!-- parent.html -->
    <template>
        <h1>To Do List</h1>
        <c-child>Buy potatoes</c-child>
        <c-child>Donate to a good cause</c-child>
        <c-child class="active">Plan a party</c-child>
    </template>
    ```
    ```css
    /* parent.css */

    h1 {
        font-size: xx-large;
    }
    ```
3. ## JS
    1. #### Public Properties
        ```html
        <!-- todoItem.html -->
        <template>
            <div class="view">
                <label>{itemName}</label>
            </div>
        </template>
        ```
        ```js
        // todoItem.js
        import { LightningElement, api } from 'lwc';
        export default class TodoItem extends LightningElement {
            @api itemName = 'New Item';
        }
        ```
        ```html
        <!-- todoApp.html -->
        <template>
            <div class="listing">
                <c-todo-item item-name="Milk"></c-todo-item>
                <c-todo-item item-name="Bread"></c-todo-item>
            </div>
        </template>
        ```
        ```js
        // todoApp.js
        const myTodo = this.template.querySelector('c-todo-item');
        myTodo.itemName // New Item
        ```
        - Use Getters and Setters to Modify Data
            ```html
            <!-- todoItem.html -->
            <template>
                {uppercaseItemName}
            </template>
            ```
            ```js
            // todoItem.js
            import { LightningElement, api, track } from 'lwc';
            export default class TodoItem extends LightningElement {
                @track
                uppercaseItemName;

                @api
                get itemName() {
                    return this.uppercaseItemName;
                }

                set itemName(value) {
                this.uppercaseItemName = value.toUpperCase();
                }
            }
            ```
        - Manage Attribute Dependencies in a Getter
            ```html
            <template>
                <c-datatable selected-rows="1,2" rows="1,2,3,4"></c-datatable>
            </template>
            ```
            ```js
            export default class Datatable extends LightningElement {
                @track state = {};
                
                @api 
                get rows() {
                    return this.state.rows;
                }
                
                
                set rows(value) {
                    this.state.rows = value;
                    
                    // Check to see if the rows have 
                    // been marked as selected.
                    if (this.state.selectedRows && !this.selectedRowsSet) {
                        this.markSelectedRows();
                        this.selectedRowsSet = true;
                    }     
                }
                
                @api 
                set selectedRows(value) {
                    this.state.selectedRows = value;
                    
                    // If rows haven’t been set,
                    // then we can't mark anything
                    // as selected. 
                    if (!this.state.rows) {
                        this.selectedRowsSet = false;
                        return;
                    }
                    
                    this.markSelectedRows();
                }
                
                
                get selectedRows() {
                    return this.state.selectedRows;
                }
                
                markSelectedRows() {
                    // Mark selected rows.
                }
            }
            ```
    2. #### Tracked Properties
        ```html
        <!-- chartBar.html -->
        <template>
            <div class="container">
                <lightning-layout vertical-align="center">
                    <lightning-layout-item>
                        {percentage}%
                    </lightning-layout-item>
                    <lightning-layout-item flexibility="grow">
                        <div class="bar" style={style}></div>
                    </lightning-layout-item>
                </lightning-layout>
            </div>
        </template>
        ```
        ```css
        /* chartBar.css */
        .container {
            overflow: hidden;
            color: #F5B041;
        }

        .bar {
            margin-left: 4px;
            height: 36px;
            background-color: #F5B041;
        }
        ```
        ```js
        // chartBar.js
        import { LightningElement, api } from 'lwc';

        export default class ChartBar extends LightningElement {
            @api percentage;

            get style() {
                return `width: ${this.percentage}%`;
            }
        }
        ```
        ```html
        <!-- apiProperty.html -->
        <template>
            <lightning-input label="Percentage" type="number" min="0" max="100"
                value={percentage} onchange={handlePercentageChange}></lightning-input>
            <c-chart-bar percentage={percentage}></c-chart-bar>
        </template>
        ```
        ```js
        // apiProperty.js
        import { LightningElement, track } from 'lwc';

        export default class ApiProperty extends LightningElement {
            @track percentage = 50;

            handlePercentageChange(event) {
                this.percentage = event.target.value;
            }
        }
        ```
    3. #### Complex Properties
        ```html
        <!-- trackObject.html -->
        <template>
            <p>object prop: {x.a}</p>
            <p>object prop: {x.b}</p>

            <button onclick={init}>Init</button>
            <button onclick={update}>Update</button>
        </template>
        ```
        ```js
        // trackObject.js
        import { LightningElement, track } from 'lwc';
        export default class TrackObject extends LightningElement {
            @track x = {
                a : "",
                b : ""
            };

            init() {
                this.x.a = "a";
                this.x.b = "b";
            }

            update() {
                this.x.a = "aa";
                this.x.b = "bb";
            }
        }
        ```
        ------------
        ```html
        <!-- trackDate.html -->
        <template>
            <p>Date: {x}</p>

            <button onclick={initDate}>Init</button>
            <button onclick={updateDate}>Update</button>
        </template>
        ```
        ```js
        // trackDate.js
        import { LightningElement, track } from 'lwc';
        export default class TrackDate extends LightningElement {
            @track x;

            initDate() {
                this.x = new Date();
            }

            updateDate() {
                this.x.setHours(7);
            }
        }
        ```
    4. #### Boolean Properties
        ```html
        <!-- bool.html -->
        <template>
            <p>show value: {show}</p>
        </template>
        ```
        ```js
        // bool.js
        import { LightningElement, api } from 'lwc';

        export default class Bool extends LightningElement {
            // Always set the default value for a boolean to false
            @api show = false;
        }
        ```
        ```html
        <!-- parent.html -->
        <template>
            <c-bool show></c-bool>
        </template>
        ```
    5. #### Private Properties
        ```js
        // todoItem.js
        import { LightningElement, api } from 'lwc';
        export default class TodoItem extends LightningElement {
            @api itemTitle = ''; // public
            itemId = '';         // private
        }
        ```
        ```js
        // todoItem.js
        import { LightningElement, api, track } from 'lwc';
        export default class TodoItem extends LightningElement {
            @api itemTitle = ''; // public
            itemId = '';         // private
            @track state = { x: 100, y: 100 };   // internal and reactive

        }
        ```
    6. #### Reflect Properties
        ```html
        /* parent.html */
        <template>
            <c-my-component title="Hover Over the Component to See Me"></c-my-component>
        </template>
        /* Generated HTML */
        <c-my-component title="HOVER OVER THE COMPONENT TO SEE ME">
            <div>Reflecting Attributes Example</div>
        </c-my-component>
        ```
        ```js
        // myComponent.js
        import { LightningElement, api, track } from 'lwc';

        export default class MyComponent extends LightningElement {
            @track privateTitle;

            @api
            get title() {
                return this.privateTitle;
            }

            set title(value) {
                this.privateTitle = value.toUpperCase();
                this.setAttribute('title', this.privateTitle);
            }
        }
        ```
        ```js
        // myComponent.js 
        import { LightningElement } from 'lwc';

        export default class MyComponent extends LightningElement {

            connectedCallback() {
                const tabindex = this.getAttribute('tabindex');
            
                // Set the tabindex to 0 if it hasn’t been set by the consumer.
                if (!tabindex) {
                    this.setAttribute('tabindex','0');
                }
            }
        }
        ```
    7. #### Share JS code
        ```js
        // myFunction.js
        export default myFunction () { ··· }

        // consumerComponent.js
        import myFunction from 'c/myFunction';
        ```
        -----------------
        ```js
        // mortgage.js
        const getTermOptions = () => {
            return [
                { label: '20 years', value: 20 },
                { label: '25 years', value: 25 },
            ];
        };

        const calculateMonthlyPayment = (principal, years, rate) => {
            // Logic
        };

        export { getTermOptions, calculateMonthlyPayment };
        ```
        ```js
        import { getTermOptions, calculateMonthlyPayment } from 'c/mortgage';
        ```
    8. #### Third party Libs
        ```html
        <!-- libsD3.html -->
        <template>
            <lightning-card title="LibsD3" icon-name="custom:custom19">
                <div class="slds-m-around_medium">
                    <svg 
                        class="d3"
                        width={svgWidth}
                        height={svgHeight}
                        lwc:dom="manual"
                        ></svg>
                </div>
            </lightning-card>
        </template>
        ```
        ```js
        // libsD3.js
        import { LightningElement } from 'lwc';
        import { ShowToastEvent } from 'lightning/platformShowToastEvent';
        import { loadScript, loadStyle } from 'lightning/platformResourceLoader';
        import D3 from '@salesforce/resourceUrl/d3';
        import DATA from './data';

        export default class LibsD3 extends LightningElement {
            svgWidth = 400;
            svgHeight = 400;

            d3Initialized = false;

            renderedCallback() {
                if (this.d3Initialized) {
                    return;
                }
                this.d3Initialized = true;

                Promise.all([
                    loadScript(this, D3 + '/d3.v5.min.js'),
                    loadStyle(this, D3 + '/style.css'),
                ])
                    .then(() => {
                        this.initializeD3();
                    })
                    .catch(error => {
                        this.dispatchEvent(
                            new ShowToastEvent({
                                title: 'Error loading D3',
                                message: error.message,
                                variant: 'error',
                            }),
                        );
                    });
            }

            initializeD3() {
                // Example adopted from https://bl.ocks.org/mbostock/2675ff61ea5e063ede2b5d63c08020c7
                const svg = d3.select(this.template.querySelector('svg.d3'));
                const width = this.svgWidth;
                const height = this.svgHeight;
                const color = d3.scaleOrdinal(d3.schemeDark2);

                const simulation = d3
                    .forceSimulation()
                    .force(
                        'link',
                        d3.forceLink().id(d => {
                            return d.id;
                        }),
                    )
                    .force('charge', d3.forceManyBody())
                    .force('center', d3.forceCenter(width / 2, height / 2));

                const link = svg
                    .append('g')
                    .attr('class', 'links')
                    .selectAll('line')
                    .data(DATA.links)
                    .enter()
                    .append('line')
                    .attr('stroke-width', d => {
                        return Math.sqrt(d.value);
                    });

                const node = svg
                    .append('g')
                    .attr('class', 'nodes')
                    .selectAll('circle')
                    .data(DATA.nodes)
                    .enter()
                    .append('circle')
                    .attr('r', 5)
                    .attr('fill', d => {
                        return color(d.group);
                    })
                    .call(
                        d3
                            .drag()
                            .on('start', dragstarted)
                            .on('drag', dragged)
                            .on('end', dragended),
                    );

                node.append('title').text(d => {
                    return d.id;
                });

                simulation.nodes(DATA.nodes).on('tick', ticked);

                simulation.force('link').links(DATA.links);

                function ticked() {
                    link.attr('x1', d => d.source.x)
                        .attr('y1', d => d.source.y)
                        .attr('x2', d => d.target.x)
                        .attr('y2', d => d.target.y);
                    node.attr('cx', d => d.x).attr('cy', d => d.y);
                }

                function dragstarted(d) {
                    if (!d3.event.active) {
                        simulation.alphaTarget(0.3).restart();
                    }
                    d.fx = d.x;
                    d.fy = d.y;
                }

                function dragged(d) {
                    d.fx = d3.event.x;
                    d.fy = d3.event.y;
                }

                function dragended(d) {
                    if (!d3.event.active) {
                        simulation.alphaTarget(0);
                    }
                    d.fx = null;
                    d.fy = null;
                }
            }
        }
        ```
        - multiple libs
        ```js
        Promise.all([
            loadScript(this, resourceName + '/lib1.js'),
            loadScript(this, resourceName + '/lib2.js'),
            loadScript(this, resourceName + '/lib3.js')
        ]).then(() => { /* callback */ });
        ```
    9. #### Composition
    10. #### Salesforce resource
    11. #### Lifecycle
4. ## Event
    1. #### Create and dispatch 
        ```html
        <!-- paginator.html -->
        <template>
            <lightning-layout>
                <lightning-layout-item>
                    <lightning-button label="Previous" icon-name="utility:chevronleft" onclick={previousHandler}></lightning-button>
                </lightning-layout-item>
                <lightning-layout-item flexibility="grow"></lightning-layout-item>
                <lightning-layout-item>
                    <lightning-button label="Next" icon-name="utility:chevronright" icon-position="right" onclick={nextHandler}></lightning-button>
                </lightning-layout-item>
            </lightning-layout>
        </template>
        ```
        ```js
        // paginator.js
        import { LightningElement } from 'lwc';

        export default class Paginator extends LightningElement {
            previousHandler() {
                this.dispatchEvent(new CustomEvent('previous'));
            }

            nextHandler() {
                this.dispatchEvent(new CustomEvent('next'));
            }
        }
        ```
        ------------------
        ```html
        <!-- eventSimple.html -->
        <template>
            <lightning-card title="EventSimple" icon-name="custom:custom9">
                <div class="slds-m-around_medium">
                    <p class="slds-m-vertical_medium content">Page {page}</p>
                    <c-paginator onprevious={previousHandler} onnext={nextHandler}></c-paginator>
                </div>
            </lightning-card>
        </template>
        ```
        ```js
        // eventSimple.js
        import { LightningElement, track } from 'lwc';

        export default class EventSimple extends LightningElement {
            @track page = 1;

            previousHandler() {
                if (this.page > 1) {
                    this.page = this.page - 1;
                }
            }

            nextHandler() {
                this.page = this.page + 1;
            }
        }
        ```
        - Pass Data in an Event
            ```html
            <!-- contactListItem.html -->
            <template>
                <a href="#" onclick={selectHandler}>
                    <lightning-layout vertical-align="center">
                        <lightning-layout-item>
                            <img src={contact.Picture__c}></img>
                        </lightning-layout-item>
                        <lightning-layout-item padding="around-small">
                            <p>{contact.Name}</p>
                        </lightning-layout-item>
                    </lightning-layout>
                </a>
            </template>
            ```
            ```js
            // contactListItem.js
            import { LightningElement, api } from 'lwc';

            export default class ContactListItem extends LightningElement {
                @api contact;

                selectHandler(event) {
                    // Prevents the anchor element from navigating to a URL.
                    event.preventDefault();

                    // Creates the event with the contact ID data.
                    const selectedEvent = new CustomEvent('selected', { detail: this.contact.Id });

                    // Dispatches the event.
                    this.dispatchEvent(selectedEvent);
                }
            }
            ```
            -------------
            ```html
            <!-- eventWithData.html -->
            <template>
                <lightning-card title="EventWithData" icon-name="custom:custom9">
                    <lightning-layout class="slds-m-around_medium">
                        <lightning-layout-item>
                            <template if:true={listIsNotEmpty}>
                                <template for:each={contacts.data} for:item="contact">
                                    <c-contact-list-item key={contact.Id} contact={contact} onselected={contactSelected}></c-contact-list-item>
                                </template>
                            </template>
                        </lightning-layout-item>
                        <lightning-layout-item class="slds-m-left_medium">
                            <template if:true={selectedContact}>
                                <img src={selectedContact.Picture__c}></img>
                                <p>{selectedContact.Name}</p>
                                <p>{selectedContact.Title}</p>
                                <p><lightning-formatted-phone value={selectedContact.Phone}></lightning-formatted-phone></p>
                                <p><lightning-formatted-email value={selectedContact.Email}></lightning-formatted-email></p>
                            </template>
                        </lightning-layout-item>
                    </lightning-layout>
                </lightning-card>
            </template>
            ```
            ```js
            // eventWithData.js
            import { LightningElement, wire, track } from 'lwc';
            import getContactList from '@salesforce/apex/ContactController.getContactList';

            export default class EventWithData extends LightningElement {
                @track selectedContact;

                @wire(getContactList) contacts;

                contactSelected(event) {
                    const contactId = event.detail;
                    this.selectedContact = this.contacts.data.find(contact => contact.Id === contactId);
                }

                get listIsNotEmpty() {
                    return this.contacts && Array.isArray(this.contacts.data) && this.contacts.data.length > 0;
                }
            }
            ```
    2. #### Handle 
        - Attach an Event Listener Declaratively
            ```html
            <!-- parent.html -->
            <template>
                <c-child onnotification={handleNotification}></c-child>
            </template>
            ```
            ```js
            // parent.js
            import { LightningElement } from 'lwc';
            export default class Parent extends LightningElement {
                handleNotification(){
                    // Code runs when event is received
                }
            }
            ```
        - Attach an Event Listener Programmatically
            ```html
            // parent.js
            import { LightningElement } from 'lwc';
            export default class Parent extends LightningElement {
            constructor() {
                super();
                this.template.addEventListener('notification', this.handleNotification.bind(this));
            }
            }
            ```
            ```js
            this.template.addEventListener()
            ```
        - Event Retargeting
            ```html
            <!-- myButton.html -->
            <template>
                <button>{label}</button>
            </template>
            ```
            ```html
            <c-todo-app>
            #shadow-root
                <div>
                    <p>Your To Do List</p>
                </div>
                <c-todo-item>
                #shadow-root
                    <div>
                        <p>Go to the store</p>
                    </div>
                </c-todo-item>
            </c-todo-app>
            ```
        - Listen for Changes to Input Fields
            ```html
            <!-- form.html -->
            <template>
                <input type="text" value={myValue} onchange={handleChange} />
            </template>
            ```
            ```js
            // form.js
            export default class Handler extends LightningElement {
                @track myValue = "initial value";

                handleChange(evt) {
                    const typedValue = evt.target.value;
                    const trimmedValue = typedValue.trim(); // trims the value entered by the user
                    if (typedValue !== trimmedValue) {
                        evt.target.value = trimmedValue;
                    }
                    this.myValue = trimmedValue; // updates the internal state
                }
            }
            ```
    3. #### Event propagation
    4. #### Communicate between components
5. ## Data
    1. #### Load a Record using base component
        - Display a Record Using `lightning-record-form`
            ```html
            <!-- myComponent.html -->
            <template>
                <lightning-record-form
                    record-id={recordId}
                    object-api-name="Account"
                    layout-type="Compact"
                    mode="view">
                </lightning-record-form>
            </template> 
            ```
            ```js
            // myComponent.js
            import { LightningElement, api } from 'lwc';
            export default class MyComponent extends LightningElement {
                @api recordId;
            }
            ```
        - Display a Record with a Custom Field Layout Using `lightning-record-view-form`
            ```html
            <template>
                <lightning-record-view-form
                            record-id={recordId}
                            object-api-name="Account">
                    <div class="slds-grid">
                        <div class="slds-col slds-size_1-of-2">
                            <lightning-output-field field-name="Name"></lightning-output-field>
                            <lightning-output-field field-name="Phone"></lightning-output-field>
                        </div>
                        <div class="slds-col slds-size_1-of-2">
                            <lightning-output-field field-name="Industry"></lightning-output-field>
                            <lightning-output-field field-name="AnnualRevenue"></lightning-output-field>
                        </div>
                    </div>
                </lightning-record-view-form>
            </template>
            ```
            ```js
            import { LightningElement, api } from 'lwc';
            export default class MyComponent extends LightningElement{
                // Expose a recordId property.
                @api recordId;
            }
            ```
        - Display Record Data in a Custom User Interface Using `getRecord`
            ```html
            <template>
                <lightning-record-view-form
                        record-id={recordId}
                        object-api-name={accountObject}>
                    <div class="slds-grid">
                        <div class="slds-col slds-size_1-of-2">
                            <!-- Other record data here -->
                        </div>
                        <div class="slds-col slds-size_1-of-2">
                            <lightning-formatted-text value={nameValue}
                                class="slds-text-heading_large">
                            </lightning-formatted-text>
                        </div>
                    </div>
                </lightning-record-view-form>
            </template>
            ```
            ```js
            import { LightningElement, api, wire } from 'lwc';
            /* Wire adapter to fetch record data */
            import { getRecord, getFieldValue } from 'lightning/uiRecordApi';
            import ACCOUNT_OBJECT from '@salesforce/schema/Account';
            import NAME_FIELD from '@salesforce/schema/Account.Name';

            export default class AccountViewer extends LightningElement {
                /** Id of record to display. */
                @api recordId;

                /* Expose schema objects/fields to the template. */
                accountObject = ACCOUNT_OBJECT;

                /* Load Account.Name for custom rendering */
                @wire(getRecord, { recordId: '$recordId', fields: [NAME_FIELD] })
                record;

                /** Get the Account.Name value. */
                get nameValue() {
                    return this.record.data ? getFieldValue(this.record.data, NAME_FIELD) : '';
                }
            }
            ```
    2. #### Edit a Record using base component
        - Edit a Record with Fields from a Layout Using `lightning-record-form`
            ```html
            <template>
                <lightning-record-form
                    record-id={recordId}
                    object-api-name={objectApiName}
                    columns="2"
                    mode="edit"
                    layout-type="Compact">
                </lightning-record-form>
            </template>
            ```
            ```js
            import { LightningElement, api } from 'lwc';
            export default class MyComponent extends LightningElement {
                @api recordId;
                @api objectApiName;
            }
            ```
        - Edit a Record with Specific Fields Using `lightning-record-form`
            ```html
            <template>
                <lightning-record-form
                    object-api-name={objectApiName}
                    record-id={recordId}
                    fields={fields}></lightning-record-form>
            </template>
            ```
            ```js
            import { LightningElement, api } from 'lwc';

            import ACCOUNT_FIELD from '@salesforce/schema/Contact.AccountId';
            import NAME_FIELD from '@salesforce/schema/Contact.Name';
            import TITLE_FIELD from '@salesforce/schema/Contact.Title';
            import PHONE_FIELD from '@salesforce/schema/Contact.Phone';
            import EMAIL_FIELD from '@salesforce/schema/Contact.Email';

            export default class RecordFormStaticContact extends LightningElement {
                // Flexipage provides recordId and objectApiName
                @api recordId;
                @api objectApiName;

                fields = [ACCOUNT_FIELD, NAME_FIELD, TITLE_FIELD, PHONE_FIELD, EMAIL_FIELD];
            }
            ```
        - Edit a Record with a Custom Layout Using `lightning-record-edit-form`
            ```html
            <template>
                <lightning-record-edit-form
                    object-api-name={objectApiName}
                    record-id={recordId}>
                <lightning-messages></lightning-messages>
                <div class="slds-grid">
                    <div class="slds-col slds-size_1-of-2">
                        <lightning-input-field field-name="Name"></lightning-input-field>
                        <lightning-input-field field-name="Title"></lightning-input-field>
                    </div>
                    <div class="slds-col slds-size_1-of-2">
                        <lightning-input-field field-name="Phone"></lightning-input-field>
                        <lightning-input-field field-name="Email"></lightning-input-field>
                    </div>
                </div>
                <div class="slds-m-top_medium">
                    <lightning-button type="submit" variant="brand" label="Edit Contact"></lightning-button>
                </div>
            </template>
            ```
        - Reset the Form to the Original Field Values
            ```html
            <template>
                <lightning-record-edit-form 
                    record-id={recordId}
                    object-api-name="Contact">
                        <lightning-messages></lightning-messages>
                        <lightning-input-field field-name="FirstName"></lightning-input-field>
                        <lightning-input-field field-name="LastName"></lightning-input-field>
                        <lightning-input-field field-name="Email"></lightning-input-field> 
                        <lightning-input-field field-name="Phone"></lightning-input-field> 
                        <div class="slds-align_absolute-center slds-p-around_medium">
                            <lightning-button class="slds-m-around_xx-small" label="Cancel" onclick={handleReset}></lightning-button>
                            <lightning-button class="slds-m-around_xx-small" label="Create Contact" type="submit" variant="brand" ></lightning-button>
                        </div>
                </lightning-record-edit-form>
            </template>
            ```
            ```js
            import { LightningElement } from 'lwc';

            export default class FormResetExample extends LightningElement {

                @api recordId;

                handleReset(event) {
                    const inputFields = this.template.querySelectorAll(
                        'lightning-input-field'
                    );
                    if (inputFields) {
                        inputFields.forEach(field => {
                            field.reset();
                        });
                    }
                }
            }
            ```
        - Override Default Behaviors Using Custom Events
            ```html
            <template>
                <lightning-record-edit-form
                        record-id={recordId} 
                        object-api-name="Contact"
                        onerror={handleError}>
                        <!--lightning-messages not needed here
                            since we’re displaying a toast with the error message -->
                        <!--<lightning-messages></lightning-messages>-->
                        <lightning-input-field field-name="FirstName"></lightning-input-field>
                        <lightning-input-field field-name="LastName"></lightning-input-field>
                        <lightning-input-field field-name="Email"></lightning-input-field> 
                        <lightning-input-field field-name="Phone"></lightning-input-field> 
                        <lightning-button type="submit" variant="brand" label="Create Contact"></lightning-button>    
                    </lightning-record-edit-form>
            </template>
            ```
            ```js
            import { LightningElement } from 'lwc';
            import { ShowToastEvent } from 'lightning/platformShowToastEvent';

            export default class FormErrorExample extends LightningElement {

                @api recordId;

                handleError(event){
                    console.log(event.detail);
                    this.dispatchEvent(
                        new ShowToastEvent({
                            title: 'Error creating record',
                            message: event.detail.message,
                            variant: 'error',
                        }),
                    );
                }
            }
            ```
        - Edit a Record in a Custom User Interface Using updateRecord
            ```html
            <template>
                <lightning-card title="LdsUpdateRecord" icon-name="standard:record">
                    <div class="slds-m-around_medium">
                        <template if:true={contact.data}>
                            <lightning-input label="Id" disabled value={contact.data.Id}></lightning-input>
                            <lightning-input label="First Name" value={contact.data.FirstName} data-field="FirstName" onchange={handleChange} class="slds-m-bottom_x-small"></lightning-input>
                            <lightning-input label="Last Name" value={contact.data.LastName} data-field="LastName" onchange={handleChange} class="slds-m-bottom_x-small" required></lightning-input>
                            <lightning-button label="Update Contact" variant="brand" onclick={updateContact} disabled={disabled}></lightning-button>
                        </template>
                        <template if:true={contact.error}>
                            <!-- handle Apex error -->
                        </template>
                    </div>
                </lightning-card>
            </template>
            ```
            ```js
            import { LightningElement, track, wire } from 'lwc';
            import { ShowToastEvent } from 'lightning/platformShowToastEvent';
            import { updateRecord } from 'lightning/uiRecordApi';
            import { refreshApex } from '@salesforce/apex';
            import getSingleContact from '@salesforce/apex/ContactController.getSingleContact';
            import FIRSTNAME_FIELD from '@salesforce/schema/Contact.FirstName';
            import LASTNAME_FIELD from '@salesforce/schema/Contact.LastName';
            import ID_FIELD from '@salesforce/schema/Contact.Id';

            export default class LdsUpdateRecord extends LightningElement {
                @track disabled = false;
                @track error;

                @wire(getSingleContact)
                contact;

                handleChange(event) {
                    // Display field-level errors and disable button if a name field is empty.
                    if (!event.target.value) {
                        event.target.reportValidity();
                        this.disabled = true;
                    }
                    else {
                        this.disabled = false;
                    }
                }

                updateContact() {
                    const allValid = [...this.template.querySelectorAll('lightning-input')]
                        .reduce((validSoFar, inputFields) => {
                            inputFields.reportValidity();
                            return validSoFar && inputFields.checkValidity();
                        }, true);

                    if (allValid) {
                        // Create the recordInput object
                        const fields = {};
                        fields[ID_FIELD.fieldApiName] = this.contactId;
                        fields[FIRSTNAME_FIELD.fieldApiName] = this.template.querySelector("[data-field='FirstName']").value;
                        fields[LASTNAME_FIELD.fieldApiName] = this.template.querySelector("[data-field='LastName']").value;

                        const recordInput = { fields };

                        updateRecord(recordInput)
                            .then(() => {
                                this.dispatchEvent(
                                    new ShowToastEvent({
                                        title: 'Success',
                                        message: 'Contact updated',
                                        variant: 'success'
                                    })
                                );
                                // Display fresh data in the form
                                return refreshApex(this.contact);
                            })
                            .catch(error => {
                                this.dispatchEvent(
                                    new ShowToastEvent({
                                        title: 'Error creating record',
                                        message: error.body.message,
                                        variant: 'error'
                                    })
                                );
                            });
                        }
                    else {
                        // The form is not valid
                        this.dispatchEvent(
                            new ShowToastEvent({
                                title: 'Something is wrong',
                                message: 'Check your input and try again.',
                                variant: 'error'
                            })
                        );
                    }
                }
            }
            ```
    3. #### Create a Record using base component
        - Create a Record Using `lightning-record-form`
            ```html
            <template>
                <lightning-record-form
                    object-api-name={accountObject}
                    fields={myFields}
                    onsuccess={handleAccountCreated}>
                </lightning-record-form>
            </template>
            ```
            ```js
            import { LightningElement } from 'lwc';
            import ACCOUNT_OBJECT from '@salesforce/schema/Account';
            import NAME_FIELD from '@salesforce/schema/Account.Name';
            import WEBSITE_FIELD from '@salesforce/schema/Account.Website';

            /**
            * Creates Account records.
            */
            export default class AccountCreator extends LightningElement {

                accountObject = ACCOUNT_OBJECT;
                myFields = [NAME_FIELD, WEBSITE_FIELD];

                handleAccountCreated(){
                    // Run code when account is created.
                }
            }
            ```
        - Create a Record with a Custom Field Layout Using `lightning-record-edit-form`
            ```html
            <template>
                <lightning-record-edit-form object-api-name={accountObject} onsuccess={handleAccountCreated}>
                    <lightning-messages></lightning-messages>
                    <div class="slds-grid">
                        <div class="slds-col slds-size_1-of-2">
                            <lightning-input-field field-name={nameField}></lightning-input-field>
                            <lightning-input-field field-name={websiteField}></lightning-input-field>
                        </div>
                        <div class="slds-col slds-size_1-of-2">
                            <!-- More lightning-input-field components here -->
                        </div>
                    </div>
                    <lightning-button type="submit" variant="brand" label="Create Account"></lightning-button>
                </lightning-record-edit-form>
            </template>
            ```
            ```js
            import { LightningElement } from 'lwc';
            import ACCOUNT_OBJECT from '@salesforce/schema/Account';
            import NAME_FIELD from '@salesforce/schema/Account.Name';
            import WEBSITE_FIELD from '@salesforce/schema/Account.Website';

            /**
            * Creates Account records.
            */
            export default class AccountCreator extends LightningElement {
                accountObject = ACCOUNT_OBJECT;
                nameField = NAME_FIELD;
                websiteField = WEBSITE_FIELD;

                handleAccountCreated(){
                    // Run code when account is created.
                }
            }
            ```
        - Prepopulate Field Values Using `lightning-record-edit-form`
            ```html
            <template>
            <lightning-record-edit-form object-api-name="Account">
                <lightning-input-field field-name="Name"
                                        value="My Field Value">
                </lightning-input-field>
                <lightning-button class="slds-m-top_small"
                                    type="submit"
                                    label="Create new">
                </lightning-button>
            </lightning-record-edit-form>
            </template>
            ```
            ```html
            <template>
            <lightning-record-edit-form object-api-name="Account">
                <lightning-input-field field-name="Name"
                                        value={myValue}></lightning-input-field>
                <lightning-button class="slds-m-top_small"
                                    type="submit"
                                    label="Create new"></lightning-button>
            </lightning-record-edit-form>

                <lightning-button label="Override Value"
                                onclick={overrideValue}></lightning-button>

            </template>
            ```
            ```js
            import { LightningElement, api, track } from 'lwc';
            export default class FieldValueCreateExample extends LightningElement {
            @track myValue = "My Account Name";
                overrideValue(event) {
                this.myValue = "My New Name";
            }
            }
            ```
        - Reset the Form to the Original Field Values
            ```html

            ```
            ```js

            ```
        - Create a Record in a Custom User Interface Using createRecord
            ```html

            ```
            ```js

            ```
    4. #### Form density
        - Use the Org’s Default Display Density in a Form
            ```html
            <lightning-card icon-name="standard:contact" title="record-edit-form">
                <div class="slds-p-horizontal_small">
                    <!-- Replace the record-id with your own -->
                    <lightning-record-edit-form record-id="003RM0000066Y82YAE"
                                            object-api-name="Contact"
                                            density="auto">
                        <lightning-messages></lightning-messages>
                        <lightning-input-field field-name="FirstName"></lightning-input-field>
                        <lightning-input-field field-name="LastName"></lightning-input-field>
                        <lightning-input-field field-name="Email"></lightning-input-field>
                        <lightning-input-field field-name="Phone"></lightning-input-field>
                    </lightning-record-edit-form>
                </div>
            </lightning-card> 
            ```
        - Reduce Space Between the Label and Field
            ```html
            <lightning-card icon-name="standard:contact" title="record-edit-form">
                <div class="slds-p-horizontal_small">
                    <!-- Replace the record-id with your own -->
                    <lightning-record-edit-form record-id="003RM0000066Y82YAE"
                                            object-api-name="Contact"
                                            density="compact">
                        <lightning-messages></lightning-messages>
                        <lightning-input-field field-name="FirstName" class="slds-form-element_1-col"></lightning-input-field>
                        <lightning-input-field field-name="LastName" class="slds-form-element_1-col"></lightning-input-field>
                        <lightning-input-field field-name="Email" class="slds-form-element_1-col"></lightning-input-field>
                        <lightning-input-field field-name="Phone" class="slds-form-element_1-col"></lightning-input-field>
                        
                    </lightning-record-edit-form>
                </div>
            </lightning-card>
            ```
        - Set Label Variants on Form Fields
            ```html
            <lightning-card icon-name="standard:contact" title="record-edit-form">
                <div class="slds-p-horizontal_small">
                    <!-- Replace the recordId with your own -->
                    <lightning-record-edit-form record-id="003RM0000066Y82YAE"
                                            object-api-name="Contact"
                                            density="comfy">
                        <lightning-messages></lightning-messages>
                        <lightning-input-field field-name="FirstName" variant="label-inline"></lightning-input-field>
                        <lightning-input-field field-name="LastName" variant="label-inline"></lightning-input-field>
                        <lightning-input-field field-name="Email"></lightning-input-field>
                        <lightning-input-field field-name="Phone"></lightning-input-field>
                    </lightning-record-edit-form>
                </div>
            </lightning-card>
            ```
            ```html
            <lightning-card icon-name="standard:contact" title="record-view-form">
                <div class="slds-p-horizontal_small">
                    <!-- Replace the recordId with your own -->
                    <lightning-record-view-form record-id="003RM0000066Y82YAE"
                                            object-api-name="Contact"
                                            density="comfy">
                        <lightning-messages></lightning-messages>
                        <lightning-output-field field-name="FirstName" variant="label-hidden"></lightning-output-field>
                        <lightning-output-field field-name="LastName" variant="label-hidden"></lightning-output-field>
                        <lightning-output-field field-name="Email" variant="label-hidden"></lightning-output-field>
                        <lightning-output-field field-name="Phone" variant="label-hidden"></lightning-output-field>
                    </lightning-record-view-form>
                </div>
            </lightning-card>
            ```
    5. #### Create a Data table using base component
        ```js
        public with sharing class ContactController {

            @AuraEnabled(cacheable=true)
            public static List<Contact> getContactList() {
                return [SELECT Id, FirstName, LastName, Title, Phone, Email FROM Contact LIMIT 10];
            }
        }
        ```
        ```html
        <template>
            <lightning-card title="Datatable Example" icon-name="custom:custom63">

                <div class="slds-m-around_medium">
                    <template if:true={contact.data}>
                        <lightning-datatable
                            key-field="Id"
                            data={contact.data}
                            columns={columns}
                            onsave={handleSave}
                            draft-values={draftValues}>
                        </lightning-datatable>
                    </template>
                    <template if:true={contact.error}>
                        <!-- handle Apex error -->
                    </template>
                </div>
            </lightning-card>
        </template>
        ```
        - Enable Inline Editing for a Single Row
            ```js
            import { LightningElement, wire, track } from 'lwc';
            import getContactList from '@salesforce/apex/ContactController.getContactList';
            import { updateRecord } from 'lightning/uiRecordApi';
            import { refreshApex } from '@salesforce/apex';
            import { ShowToastEvent } from 'lightning/platformShowToastEvent';
            import FIRSTNAME_FIELD from '@salesforce/schema/Contact.FirstName';
            import LASTNAME_FIELD from '@salesforce/schema/Contact.LastName';
            import ID_FIELD from '@salesforce/schema/Contact.Id';


            const COLS = [
                { label: 'First Name', fieldName: 'FirstName', editable: true },
                { label: 'Last Name', fieldName: 'LastName', editable: true },
                { label: 'Title', fieldName: 'Title' },
                { label: 'Phone', fieldName: 'Phone', type: 'phone' },
                { label: 'Email', fieldName: 'Email', type: 'email' }
            ];
            export default class DatatableUpdateExample extends LightningElement {

                @track error;
                @track columns = COLS;
                @track draftValues = [];

                @wire(getContactList)
                contact;

                handleSave(event) {

                    const fields = {};
                    fields[ID_FIELD.fieldApiName] = event.detail.draftValues[0].Id;
                    fields[FIRSTNAME_FIELD.fieldApiName] = event.detail.draftValues[0].FirstName;
                    fields[LASTNAME_FIELD.fieldApiName] = event.detail.draftValues[0].LastName;

                    const recordInput = {fields};

                    updateRecord(recordInput)
                    .then(() => {
                        this.dispatchEvent(
                            new ShowToastEvent({
                                title: 'Success',
                                message: 'Contact updated',
                                variant: 'success'
                            })
                        );
                        // Clear all draft values
                        this.draftValues = [];

                        // Display fresh data in the datatable
                        return refreshApex(this.contact);
                    }).catch(error => {
                        this.dispatchEvent(
                            new ShowToastEvent({
                                title: 'Error creating record',
                                message: error.body.message,
                                variant: 'error'
                            })
                        );
                    });
                }
            }
            ```
        - Enable Inline Editing for Multiple Rows
            ```js
            handleSave(event) {
                const recordInputs =  event.detail.draftValues.slice().map(draft => {
                    const fields = Object.assign({}, draft);
                    return { fields };
                });

                const promises = recordInputs.map(recordInput => updateRecord(recordInput));
                Promise.all(promises).then(contacts => {
                    this.dispatchEvent(
                        new ShowToastEvent({
                            title: 'Success',
                            message: 'Contacts updated',
                            variant: 'success'
                        })
                    );
                    // Clear all draft values
                    this.draftValues = [];

                    // Display fresh data in the datatable
                    return refreshApex(this.contact);
                }).catch(error => {
                    // Handle error
                });
            }
            ```
    6. #### Usage
        - Get Record Type Id
            ```html
            <template>
                <lightning-card title="Record Form with Record Type" icon-name="standard:account">
                    <div if:true={objectInfo.data} class="slds-m-around_medium">
                        <lightning-record-form object-api-name={objectApiName} 
                            record-type-id={recordTypeId}
                            fields={fields}>
                        </lightning-record-form>
                    </div>
                </lightning-card>
            </template>
            ```
            ```js
            import { LightningElement, api, wire, track } from 'lwc';
            import { getObjectInfo } from 'lightning/uiObjectInfoApi';
            import ACCOUNT_OBJECT from '@salesforce/schema/Account';
            import NAME_FIELD from '@salesforce/schema/Account.Name';
            import PHONE_FIELD from '@salesforce/schema/Account.Phone';
            import INDUSTRY_FIELD from '@salesforce/schema/Account.Industry';

            export default class RecordFormWithRecordType extends LightningElement {
                // Flexipage provides recordId and objectApiName
                @api recordId;
                @api objectApiName;

                @track objectInfo;

                // Define fields to display in form
                // Industry field is a picklist
                fields = [NAME_FIELD, PHONE_FIELD, INDUSTRY_FIELD];

                @wire(getObjectInfo, { objectApiName: ACCOUNT_OBJECT })
                objectInfo;

                get recordTypeId() {
                    const rtis = this.objectInfo.data.recordTypeInfos;
                    return Object.keys(rtis).find(rti => rtis[rti].name === 'Special Account');
                }
            }
            ```
        - Handle a Custom Event on a Form
            ```html
            <!-- wrapper.html -->
            <template>
                <c-account-creator onnewrecord={handleNewRecord}></c-account-creator>
            </template>
            ```
            ```js
            // wrapper.js
            import { LightningElement, track } from 'lwc';

            export default class Wrapper extends LightningElement {
                @track recordId;

                /**
                * Handles the new record event.
                */
                handleNewRecord(evt) {
                    const recordId = evt.detail.data.id;
                    this.recordId = recordId;
                }
            }
            ```
            ```html
            <!-- c-account-creator -->
            <template>
                <lightning-record-form object-api-name={accountObject}
                    fields={accountFields}
                    mode="edit"
                    onsuccess={handleAccountCreated}>
                </lightning-record-form>
                <span class="slds-m-around_small status">{createStatus}</span>
            </template>
            ```
            ```js
            import { LightningElement, track } from 'lwc';
            import ACCOUNT_OBJECT from '@salesforce/schema/Account';
            import NAME_FIELD from '@salesforce/schema/Account.Name';
            import WEBSITE_FIELD from '@salesforce/schema/Account.Website';

            /**
            * Creates Account records.
            */
            export default class AccountCreator extends LightningElement {
                /** Status message when creating an Account. */
                @track createStatus = '';

                accountObject = ACCOUNT_OBJECT;

                accountFields = [NAME_FIELD, WEBSITE_FIELD];

                /** Handles successful Account creation. */
                handleAccountCreated(evt) {
                    this.createStatus = `Account record created. Id is ${evt.detail.id}.`;

                    const event = new CustomEvent('newrecord', {
                        detail: { data: evt.detail },
                    });
                    this.dispatchEvent(event);
                }
            }
            ```
    7. #### Get Record using wired service
        ```html
        <!-- wireGetRecordDynamicContact.html -->
        <template>
            <lightning-card title="My Contact Record" icon-name="standard:contact">
                <template if:true={contact.data}>
                    <div class="slds-m-around_medium">
                        <p>{name}</p>
                        <p>{title}</p>
                        <p><lightning-formatted-phone value={phone}></lightning-formatted-phone></p>
                        <p><lightning-formatted-email value={email}></lightning-formatted-email></p>
                    </div>
                </template>
                <template if:true={contact.error}>
                    <c-error-panel errors={contact.error}></c-error-panel>
                </template>
            </lightning-card>
        </template>
        ```
        ```js
        // wireGetRecordDynamicContact.js
        import { LightningElement, api, wire } from 'lwc';
        import { getRecord } from 'lightning/uiRecordApi';

        const FIELDS = [
            'Contact.Name',
            'Contact.Title',
            'Contact.Phone',
            'Contact.Email',
        ];

        export default class WireGetRecordDynamicContact extends LightningElement {
            @api recordId;

            @wire(getRecord, { recordId: '$recordId', fields: FIELDS })
            contact;

            get name() {
                return this.contact.data.fields.Name.value;
            }

            get title() {
                return this.contact.data.fields.Title.value;
            }

            get phone() {
                return this.contact.data.fields.Phone.value;
            }

            get email() {
                return this.contact.data.fields.Email.value;
            }
        }
        ```
    8. #### Create Record using wired service
        ```html
        <template>
            <lightning-card title="LdsCreateRecord" icon-name="standard:record">
            <div class="slds-m-around_medium">
                <lightning-input label="Id" disabled value={accountId}></lightning-input>
                <lightning-input label="Name" onchange={handleNameChange} class="slds-m-bottom_x-small"></lightning-input>
                <lightning-button label="Create Account" variant="brand" onclick={createAccount}></lightning-button>
            </div>
            </lightning-card>
        </template> 
        ```
        ```js
        import { LightningElement, track } from 'lwc';
        import { createRecord } from 'lightning/uiRecordApi';
        import { ShowToastEvent } from 'lightning/platformShowToastEvent';
        import ACCOUNT_OBJECT from '@salesforce/schema/Account';
        import NAME_FIELD from '@salesforce/schema/Account.Name';

        export default class LdsCreateRecord extends LightningElement {
            @track accountId;
            name = '';

            handleNameChange(event) {
                this.accountId = undefined;
                this.name = event.target.value;
            }
            createAccount() {
                const fields = {};
                fields[NAME_FIELD.fieldApiName] = this.name;
                const recordInput = { apiName: ACCOUNT_OBJECT.objectApiName, fields };
                createRecord(recordInput)
                    .then(account => {
                        this.accountId = account.id;
                        this.dispatchEvent(
                            new ShowToastEvent({
                                title: 'Success',
                                message: 'Account created',
                                variant: 'success',
                            }),
                        );
                    })
                    .catch(error => {
                        this.dispatchEvent(
                            new ShowToastEvent({
                                title: 'Error creating record',
                                message: error.body.message,
                                variant: 'error',
                            }),
                        );
                    });
            }
        }
        ```
    9. #### Handle error
        ```html
        <template>
            <lightning-card title="Handle Error" icon-name="standard:contact">
                <template if:true={error}>
                    <p>{error}</p>
                </template>
            </lightning-card>
        </template>
        ```
        ```js
        import { LightningElement, api, wire, track } from 'lwc';
        import { getRecord } from 'lightning/uiRecordApi';

        const fields = [
            // This invalid field causes @wire(getRecord) to return an error
            'Contact.invalidField' 
        ];

        export default class WireGetRecordDynamicContact extends LightningElement {
            @api recordId;

            @track error;

            @wire(getRecord, { recordId: '$recordId', fields })
            wiredRecord({error, data}) {
                if (error) {
                    this.error = 'Unknown error';
                    if (Array.isArray(error.body)) {
                        this.error = error.body.map(e => e.message).join(', ');
                    } else if (typeof error.body.message === 'string') {
                        this.error = error.body.message;
                    }
                    this.record = undefined;
                } else if (data) {
                    // Process record data
                }
            }
        }
        ```
    10. #### Wired service with base component
        - Create a Custom Form
            ```html
            <template if:true={contact.data}> 
                <div class="slds-m-around_medium">
                    <lightning-input-name
                            label="Contact Name"
                            first-name={firstname}
                            last-name={lastname}
                            salutation={salutation}
                            options={salutations}
                            class="slds-m-bottom_x-small"
                            required>
                    </lightning-input-name>
                </div>
            </template>
            ```
            ```js
            import { LightningElement, api, wire } from 'lwc';
            import { getRecord } from 'lightning/uiRecordApi';
            import { getPicklistValues } from 'lightning/uiObjectInfoApi';

            import FIRSTNAME_FIELD from '@salesforce/schema/Contact.FirstName';
            import LASTNAME_FIELD from '@salesforce/schema/Contact.LastName';
            import SALUTATION_FIELD from '@salesforce/schema/Contact.Salutation';

            const namefields = [FIRSTNAME_FIELD, LASTNAME_FIELD, SALUTATION_FIELD];

            export default class GetContactName extends LightningElement {
                @api recordId; // provided by the contact record page

                @wire(getPicklistValues, { recordTypeId: '012000000000000AAA', fieldApiName: SALUTATION_FIELD })
                salutationValues;
                
                @wire(getRecord, { recordId: '$recordId', fields: namefields })
                contact;
                
                get firstname() {
                    return this.contact.data.fields.FirstName.value;
                }

                get lastname() {
                    return this.contact.data.fields.LastName.value;
                }

                get salutation() {
                    return this.contact.data.fields.Salutation.value;
                }

                // creates the options array for lightning-input-name
                get salutations() {
                    let salutationOptions = [];
                    Object.entries(this.salutationValues.data.values).forEach(val => {
                        let values = val[1];
                        salutationOptions.push({ 'label': values.label, 'value': values.value });
                    }); 
                    return salutationOptions;
                }
            }
            ```
        - Display Record Data Using Base Components
            ```html
            <template>
                <lightning-card
                    title="Display Contact Address"
                    icon-name="standard:contact"> 
                    <template if:true={contact.data}> 
                        <div class="slds-m-around_medium">  
                            <lightning-formatted-address
                                street={street}
                                city={city}
                                country={country}
                                province={state}
                                postal-code={postal}
                                show-static-map
                            ></lightning-formatted-address>
                        </div>
                    </template>
                </lightning-card>
            </template>
            ```
            ```js
            import { LightningElement, api, wire } from 'lwc';
            import { getRecord } from 'lightning/uiRecordApi';

            import STREET_FIELD from '@salesforce/schema/Contact.MailingStreet';
            import CITY_FIELD from '@salesforce/schema/Contact.MailingCity';
            import STATE_FIELD from '@salesforce/schema/Contact.MailingState';
            import COUNTRY_FIELD from '@salesforce/schema/Contact.MailingCountry';
            import POSTAL_FIELD from '@salesforce/schema/Contact.MailingPostalCode';

            const FIELDS = [STREET_FIELD, CITY_FIELD, STATE_FIELD, COUNTRY_FIELD, POSTAL_FIELD];

            export default class GetContactAddress extends LightningElement {
                @api recordId; // provided by the contact record page

                @wire(getRecord, { recordId: '$recordId', fields: FIELDS })
                contact;

                get street() {
                    return this.contact.data.fields.MailingStreet.value;
                }

                get city() {
                    return this.contact.data.fields.MailingCity.value;
                }

                get state() {
                    return this.contact.data.fields.MailingState.value;
                }

                get country() {
                    return this.contact.data.fields.MailingCountry.value;
                }

                get postal() {
                    return this.contact.data.fields.MailingPostalCode.value;
                }
            }
            ```
    11. #### Call Apex method

