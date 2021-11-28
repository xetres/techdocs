

ClearTH™ stands for “Clearing Test Harness” and is a test automation tool written in Java whose primary target is testing of business scenarios for various post trade systems. ClearTH allows users to send messages to the System and automatically compare System responses with expected results. Also it automates may actions in a System web front-end, can perform verefication of data in databases, execute various queries and automate many other actions. Scenarios are written in Matrix format.

ClearTH is a multi-user web application..

ClearTH is developed to automatize testing of Clearing and settlement systems. ClearTH has Core (common functionality) and implementations (extensions to meet specific project features). ClearTH Core includes:

    automation – features to automate testing using scripts. Automation uses terms of schedulers, steps, matrices and actions;
    connectivity – feature to connect to and interact with Client system. It usually means connections to IBM MQ and for a some projects connections to Client system GUI and/or DB;
    send messages – small feature to send messages to Client system via MQ connection by hand.Is used for hand and/or negative testing;
    tools – few features to make work with ClearTH more convenient. It includes various parsers, converters etc. integrated into ClearTH. For each new project (i.e. new Client system) we take ClearTH Core as a library and implement extensions based on it to meet project requirements and handle specific cases.

Here is the architecture diagram which shows structure of ClearTH and how it interacts with Client system:

NOTE: ClearTH Core doesn’t have any internal DB, thus the DB block shown inside ClearTH above is optional.

The main purpose of ClearTH is test automation. Testing scenarios can be complicated. Multiple test scenarios are usually required to cover the behavior of a system under test to verify its quality. The tests need to be run multiple times for every new version of a system under test. When automated, the testing process becomes more effective, minimizing the human factor and producing more stable results.

The idea of test automation in ClearTH is based on four concepts:

    Scheduler – an entity that contains configuration for test automation. It includes Matrices to run and a Schedule – an ordered set of Global steps referred to in the Matrices. The Schedule usually corresponds to steps of a business day (or many business days) in the system under test;
    Global step – part of a Schedule. It identifies a group of Actions;
    Matrix – a test script that reflects a scenario to be automated. It is written in Matrix language and consists of Actions. Please refer to the “Exactpro ClearTH Matrix Language Guide” manual for additional details;
    Action – a simple action to perform such as sending one message or comparing expected and actual data objects or similar. Each Action belongs to one Global step, has an ID (it is unique within the Matrix), a name (shows what the Action is expected to perform) and a set of parameters that affect Action’s behaviour.

Main part of ClearTH is Automation. To automate testing of Client system ClearTH uses scripts (aka matrices), provided by Testers. Each matrix consists of descriptions of actions which should be done to make some test. Matrices are multi-header CSV files, each action is one string of such file. Here is the example of a matrix: #Id,#GlobalStep,#Action,#Execute,#Comment,#Param1,#Param2 id1,Step1,testaction,true,"Action one!","Ha!","What?" id2,Step3,testaction,false,"Action two!","SetPrty","DEFCON" #Id,#GlobalStep,#Action,#Execute,#Comment,#Value Id3,Step3,testaction,false,"Action three!","Here is the value"

It includes the actions – id1, id2 and id3. Two of them have common header, so they can stay one after another. Action id3 has different header, that’s why its header is described before the action. Action IDs should be unique within matrix.

To automate work (and testing!) with Client system, ClearTH needs to have a schedule which reflects structure of actual business day in the System. Schedule consists of steps, as a business day does. ClearTH can have many schedules; each of them is a part of different Scheduler, one schedule per Scheduler.

Scheduler also includes matrices to execute within its schedule. Scheduler can have many matrices which will be exected “in parallel”. Actually actions of these matrices will be executed one after another, but all the actions of all the matrices from the Scheduler will form one list of actions, divided by steps. That’s why we say “in parallel” and that’s why it’s not direct meaning.

As stated above, each Scheduler can have its own schedule (a list of steps) and its own list of matrices to execute.

Each matrix as you already know consists of actions.

Any action consists of parameters list, which can be divided to “system” parameters (ALWAYS present in any action and are required by ClearTH) and input parameters (SPESIFIC to certain action type and test case).

Action will be executed in the step defined by action’s “Global step” system parameter. To completely understand how actions are linked with a schedule and how one list of actions will be formed for all Scheduler matrices, consider the following example:

Let’s say that we have Scheduler with the following steps defined:

    Upload
    Verify
    Settle Scheduler also contains two matrices – matrix1 and testmatrix.

matrix1 consists of the following actions (only parameters needed in this example are mentioned, action types are fictitious): #Id,#GlobalStep,#Action id1,Upload,UploadTrade id2,Upload,UploadTrade id3,Settle,HoldPosition

testmatrix consists of the following actions: upl1,Upload,UploadTrade ver1,Verify,CheckPosition upl2,Upload,UploadTrade set1,Settle,ReleasePosition ver2,Verify,CheckPosition

So Scheduler will have the following list of actions divided by steps:

    Upload 1.1. id1 1.2. id2 1.3. upl1 1.4. upl2
    Verify 2.1. ver1 2.2. ver2
    Settle 3.1. id3 3.2. set1

The order of Global steps in the Scheduler defines the order of execution for Actions grouped by Global step names. The order of Actions within a Global step is defined by the order of these Actions within the Matrix.

Therefore the Matrices are executed within Scheduler according to Scheduler configuration. During execution a report is generated to show how the test goes: if any Actions were failed and why if they were, how long it took to execute a particular Action, etc. If an Action cannot be executed properly due to lack of mandatory parameters, it will be reflected in the report. If some comparison was performed by the Action, a comparison table will be shown. The report shows execution status for each Action: PASSED if the Action was executed successfully and FAILED if something went wrong or if expected and actual results differ. The report also contains overall results for each Global step and for the entire scenario. Each Matrix has a separate report for each new test run.

Schedulers support the execution of multiple Matrices. There are two options:

    Single run: all Actions from all Matrices are grouped by Global step names and executed one-by-one. Actions from different Matrices are separated from each other, so they can only interact with Actions that are in the same Matrix;
    Sequential run: Matrices are executed one-by-one in the defined order . For each Matrix the Global steps are executed from the first one to the last one. When execution of a Matrix is completed, a pause is done and the next Matrix starts executing.

It was the description of ClearTH basis. Of course, many features, remain undescribed, but now you at list have something to start with.
