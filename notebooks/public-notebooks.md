# Community \(Public\) Notebooks

{% hint style="info" %}
Gradient Community Notebooks were launched as a public beta in October 2019. We'd love to hear your feedback on Community Notebooks as we work to stabilize and round out the UX of this new product offering. 
{% endhint %}

## Gradient Community Notebooks

Gradient Community Notebooks allow you to create, run, and share your Jupyter Notebooks with the world.

The goal is to offer a product that makes it free, easy, and fun to share your work with others, to discover and fork notebooks from other users, and to introduce social features to Gradient. We are working to release Community Notebooks to the general public in the coming weeks!

Gradient Community Notebooks can be viewed by anyone, with or without a Paperspace account.

Community Notebooks include notebooks that are run on the [Free Instances](../instances/free-instances.md) – these will be Public by default – as well as any notebook that you make Public by sharing, even if it's paid.

### Making a Notebook Public or Private

Navigate to your notebook in and open the Notebook IDE. In the top right there is a "Share" button. You can then click the toggle \(pictured green already below\) to make the notebook Public or Private.

![](../.gitbook/assets/share.png)

### Viewing a Community Notebook, & Static Versions

Anyone can view a Notebook that has been made Public just by visiting the URL! :\)

When you visit a Community Notebook, you'll always see the last Stopped version of that notebook. So if the notebook owner is currently running it, you'll see a message that says:`Note: You are viewing the static version of this notebook. Run the notebook to see the interactive live version.`

Don't be alarmed – this is expected behavior. Due to a limitation in Jupyter notebooks, we can't show you the actively running version.

If the notebook is not currently running, you'll see the most recent Stopped version, which is what you would expect to see.

Other users will be able to view your notebook \(including having access to its container layers\) only once: 1\) a Community Notebook has been Stopped; 2\) its teardown completes behind the scenes.

This means that if you're trying to share a Gradient Community Notebook via its Public URL, but you've never once Stopped it, users will not be able to view it and instead will see the message: `This Notebook is not in a viewable state.`

### Forking Community Notebooks

You can fork any Gradient Community Notebook by clicking the green Play button at the top of that Community Notebook's page. For now, this will fork the notebook into your private workspace. Soon it will allow you to fork into a team of your choice.

_Note: By making your notebook Public, the underlying container will be able to be forked to other users' accounts, including if they are within a private container registry. So, as with anytime you would share a container, be sure to remove any sensitive data, such as API keys or secrets, before making that notebook Public!_

### [Public Profiles](../paperspace-account/gradient-public-profiles.md)

With this launch, you can now create a [Public Profile](../paperspace-account/gradient-public-profiles.md) and a user or team handle. This is the start of social features on Gradient!

### Limitations

Community Notebooks take advantage of the newly released [Free Instances](../instances/free-instances.md) on Gradient. This means that they will be Public-only and will auto-shutdown after 6 hours.

## More Info

Learn more about [Persistent Storage for Gradient Community Notebooks on free instances](../instances/free-instances.md#persistent-storage-for-free-instances).

Learn more about [Free Instances](../instances/free-instances.md).

Learn more about the various [Instance Types](../instances/instance-types.md).

Learn more about our [Instance Tiers](../instances/instance-tiers.md).

