# Fabric item creation policy demo 

## What this demonstrates

In Microsoft Fabric, anyone with the right workspace role can usually create **any** type of item in a workspace:
lakehouses, notebooks, warehouses, data agents, and so on. [**Fabric policies**](https://learn.microsoft.com/fabric/governance/fabric-policies-overview) let you control that. A **policy set**
with *Allow item creation* rules, turned on for a **capacity**, decides which item types can be created in which
workspaces on that capacity. Anything the rules don't allow is blocked.

This demo shows that in action. It sets up three workspaces and a policy set so that:

* one workspace is limited to **Data Engineering** items (Lakehouse, Data pipeline, Notebook), so a **Data agent
  is blocked** there;
* a second workspace gets the same items **plus Data agent**, showing how to allow an exception for one workspace;
* every **other** workspace on the capacity, including the one that stores the policy set, keeps working as before.

Short Fabric notebooks do the setup and the cleanup, with plain-language messages at every step, so you don't need
to write code.

## The notebooks

Pick the setup notebook that fits your situation:

| Notebook | Use it to | What you type in | Permissions you need |
| --- | --- | --- | --- |
 --- |
| [setup_policy_existing_workspaces.ipynb](./setup_policy_existing_workspaces.ipynb) | Set up the policy when your three workspaces **already exist** | The three workspace names | **Contributor** or higher on Workspace 1<br>**Viewer** or higher on Workspaces 2 and 3<br>**Policies** tenant setting turned on for you<br>*Optional:* **Capacity Admin**. Without it, the policy set is still created, and the notebook prints a message to send to your Capacity Admin to turn it on. |
| [setup_policy_new_workspaces.ipynb](./setup_policy_new_workspaces.ipynb) | Set up the policy and **create** the workspaces on a capacity | A capacity ID and the three workspace names | **Capacity Contributor** or higher<br>**Create workspaces** and **Policies** tenant settings turned on for you<br>*Optional:* **Capacity Admin**. Without it, the policy set is still created, and the notebook prints a message to send to your Capacity Admin to turn it on. |
| [cleanup_policy.ipynb](./cleanup_policy.ipynb) | **Turn off** the policy, and optionally **delete** the workspaces | The same names you used for setup, and `DELETE_WORKSPACES` (`False` by default) | **Capacity Admin**<br>*Optional:* **Admin** on each workspace, only if you delete the workspaces (`DELETE_WORKSPACES = True`) |

## How to set up and run the demo in your own workspaces

1. **Download the notebooks to your local machine.** From [the notebooks listed above](#the-notebooks), download
   the setup notebook that fits your situation, [policy_setup_functions.ipynb](./policy_setup_functions.ipynb),
   and [cleanup_policy.ipynb](./cleanup_policy.ipynb) for later cleanup.
2. In the Fabric portal, open any workspace, and select **Import** > **Notebook** > **From this computer**. Choose
   the notebook you want **and** `policy_setup_functions.ipynb`. Both must be in the **same workspace**, and keep
   the name `policy_setup_functions`. For cleanup with workspace deletion, use a workspace that isn't one of the
   three being deleted.
3. Open your notebook, and type your values in the **Settings** cell.
4. Select **Run all**.

If the **Load the setup functions** cell fails because the notebook can't be found, import
`policy_setup_functions.ipynb` into the same workspace, and run again.

## What you'll see

After setup, wait up to 15 minutes, and then try creating items:

| Workspace | Default name | Purpose | Can create |
| --- | --- | --- | --- |
| **Workspace 1** - policy store | `Policy-Admin-Store` | Holds the **policy set** item. Keeping it in its own workspace separates governance from the workspaces being restricted. | ✅ Everything - it isn't restricted (Rule 3) |
| **Workspace 2** - restricted | `Policy-DA-Blocked` | Shows the **restriction**: only Data Engineering items can be created. | ✅ Lakehouse, Data pipeline, Notebook<br>❌ Data agent, Warehouse, everything else |
| **Workspace 3** - restricted, with an exception | `Policy-DA-Allowed` | Shows an **exception**: the same as Workspace 2, plus Data agent. | ✅ Lakehouse, Data pipeline, Notebook, **Data agent**<br>❌ Warehouse, everything else |
| Any other workspace on the capacity | - | Isn't named in the policy, so it's unaffected. | ✅ Everything (Rule 3) |

Workspaces 2 and 3 must be on the **same** Fabric capacity, because the policy set is turned on for one capacity.
Workspace 1 can be on any Fabric capacity. You can use your own names in the notebooks' **Settings** cell.

If you aren't a Capacity Admin, the setup notebooks still create the policy set and its rules, and then print a
ready-to-send message asking your Capacity Admin to turn it on.

## Clean up

[cleanup_policy.ipynb](./cleanup_policy.ipynb) undoes the setup in two steps:

1. **Turn off the policy set** stored in Workspace 1, so item creation is no longer restricted (within 15 minutes).
   If it's already off or doesn't exist, nothing happens. If this step fails, the notebook stops and **nothing is
   deleted**.
2. **Delete the workspaces** - only when `DELETE_WORKSPACES = True`. It deletes all three workspaces and
   **everything in them**, including the policy set, and it can't be undone. With the default
   `DELETE_WORKSPACES = False`, the workspaces are kept, and the policy set stays in Workspace 1 (turned off), so you
   can turn it on again later by running a setup notebook.

## Appendix: How the notebooks are organized

All three notebooks load [policy_setup_functions.ipynb](./policy_setup_functions.ipynb) with
`%run policy_setup_functions`. It holds the code for **every step**, so each notebook is just a list of steps, with
one line of code per step:

| Notebook | Steps (functions in `policy_setup_functions`) |
| --- | --- |
| `setup_policy_existing_workspaces` | `find_existing_workspaces` > `create_policy_set` > `add_rules` > `turn_on_policy_set` |
| `setup_policy_new_workspaces` | `check_capacity` > `create_workspaces` > `create_policy_set` > `add_rules` > `turn_on_policy_set` |
| `cleanup_policy` | `turn_off_policy_set` > `delete_workspaces` |

You never run `policy_setup_functions` yourself. Its code cells are collapsed, and each section says which notebook
uses it.

Each step prints what it did. If something goes wrong, the notebook **stops** and tells you what happened and
what to do, such as which permission is missing and whom to ask. It's safe to run any notebook again: setup reuses
existing workspaces and the policy set and replaces the rules (never duplicates them), and cleanup skips anything
that's already gone.
