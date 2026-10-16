# My Microsoft Fabric Samples, Demos and Utilities

Companion repository for the blog post [Using Microsoft Fabric Policies to Govern Workspace Item Creation](https://isinghrana.medium.com/using-microsoft-fabric-policies-to-govern-workspace-item-creation-101ae66f5f7f).

Hands-on demos and utilities for [Microsoft Fabric](https://learn.microsoft.com/fabric/). Each demo is in its own
folder, with a README that explains what it shows and how to run it.

## Demos

| Demo | What it shows |
| --- | --- |
| [Fabric item creation policy](./fabric-policy-item-creation/) | Use a Fabric policy set with *Allow item creation* rules to control which item types can be created in which workspaces on a capacity. Setup and cleanup run from Fabric notebooks, so you don't need to write code. |

## What you need

* Access to a [Microsoft Fabric](https://app.fabric.microsoft.com/) tenant and a Fabric capacity.
* The roles and tenant settings listed in each demo's README.

## License

This project is licensed under the [MIT License](./LICENSE).
