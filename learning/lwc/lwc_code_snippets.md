# Tutorials
1. ## Template
    1. #### data binding in Template
    ```
    <!-- hello.html -->
    <template>
        Hello, {greeting}!
    </template>
    ```
    ```
    // hello.js
    import { LightningElement } from 'lwc';

    export default class Hello extends LightningElement {
        greeting = 'World';
    }
    ```
    --------------
    ```
    <!-- helloBinding.html -->
    <template>
        <p>Hello, {greeting}!</p>
        <lightning-input label="Name" value={greeting} onchange={handleChange}></lightning-input>
    </template>
    ```
    ```
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
    ```
    <!-- helloExpressions.html -->
    <template>
        <div class="slds-m-around_medium">
            <lightning-input name='firstName' label="First Name" onchange={handleChange}></lightning-input>
            <lightning-input name='lastName' label="Last Name" onchange={handleChange}></lightning-input>
            <p class="slds-m-top_medium">Uppercased Full Name: {uppercasedFullName}</p>
        </div>
    </template>
    ```
    ```
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
    ```
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
    ```
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
    ```
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
    ```
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
    ```
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
    ```
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
    ```
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
    ```
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
    ```
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
    ```
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
    ```
    /* myWebComponent.css */
    div {
        margin-right: var(--lwc-spacingSmall);
    }
    ```
    3. Basic CSS way
    ```
    <!–- child.html-->
    <template>
    <h1>To Do Item</h1>
    <slot></slot>
    </template>
    ```
    ```
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
    ```
    <!-- parent.html -->
    <template>
        <h1>To Do List</h1>
        <c-child>Buy potatoes</c-child>
        <c-child>Donate to a good cause</c-child>
        <c-child class="active">Plan a party</c-child>
    </template>
    ```
    ```
    /* parent.css */

    h1 {
        font-size: xx-large;
    }
    ```