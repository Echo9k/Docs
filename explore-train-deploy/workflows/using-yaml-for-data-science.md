# Using YAML for Data Science

YAML provides a powerful and precise configuration for a data science pipeline to run to a production standard, and as such it needs care to specify it correctly. While it is relatively intuitive to see what is going on at a high level, there is a lot going on in the details. The combination of YAML syntax, Gradient actions, implicit information, and the conceptual variety of steps being performed for data science \(data preparation, model training, model deployment, etc.\), means that it can be tricky to get everything right at first.

We therefore collect here some examples likely to come up in practice when implementing data science workflows.

### Indentation & syntax highlighting

YAML requires precise indentation, and tabs are not allowed. The Gradient Notebook allows easy creation and editing of YAML files, with aids such as visual prompts for indentation, and syntax highlighting.

![](../../.gitbook/assets/yaml_highlighting.png)

### Multi-line commands

Many jobs within a workflow spec will not be complex enough to require a script to be imported, but will need several commands. The YAML syntax `|-` \(pipe, dash\) sequence allows this to be laid out such that they don't all have to be on one line, and can be written with their arguments.

```yaml
    uses: container@v1
    with:
      args:
        - bash
        - -c
        - |-
          pip install scipy==1.3.3
          pip install requests==2.22.0
          pip install Pillow==6.2.1
          cp -R /inputs/repo/stylegan2 /stylegan2
          cd /stylegan2
```

### Directory names

Jobs have inputs and outputs which results in some standard directory names that need to be given correctly. The inputs to a job are in `/inputs`, and the outputs from a job are in `/outputs`. The outputs from one job can become the inputs to the next, so what was, e.g., `/outputs/my_directory` becomes `/inputs/my_directory` in the next job.

The name of your output must match the directory name, e.g., in the example the output is named my-dataset \(line 10\), so for the output to be seen the directory under `/outputs` must also be named my-dataset \(line 7\).

```yaml
my-job:
  uses: container@v1
  with:
    args:
      - bash
      - '-c'
      - cp -R /my-trained-model /outputs/my-dataset
    image: bash:5
  outputs:
    my-dataset:
      type: dataset
      with:
        id: my-dataset-id  
```

### Inputs directory is not writable

The inputs to a job are in the /inputs directory, and this directory is not writeable. So to change the contents of this directory it needs to be copied out of /inputs. For example, the StyleGAN2 onboarding workflow job to generate images contains the commands

```yaml
cp -R /inputs/repo/stylegan2 /stylegan2
cd /stylegan2
```

### Directory paths may be unexpected

A wrong directory path will cause the workflow to fail with `No such file or directory`, or some related consequence, so it is important to be pointing to the right places.

An example of a not-necessarily-intuitive path is when the `git-checkout@v1` action is used to mount that repo to a volume, e.g.,

```yaml
CloneCatRepo:
  inputs:
    repoCat:
      type: volume
  uses: git-checkout@v1
  with:
    url: https://github.com/nmb-paperspace/cat-subsample
    username: nmb-paperspace
  env:
    GIT_PASSWORD: secret:GIT_PASSWORD
```

but then the repo content, here `cat_images_tfrecords.tgz` is put directly into `/inputs`, so the path is `/inputs/repoCat/cat_images_tfrecords.tgz`, without the original repo URL.

### Dataset identifiers

Currently there is an artifact in the system where a dataset must be referred to by its ID, but its ID is not known until the dataset is generated. The dataset must therefore be generated outside the given YAML file. This can be done in the GUI under the Data tab by clicking Add.

![](../../.gitbook/assets/create_dataset.png)

### Dataset versions

Datasets can be referred to by their versions \(like `defghijklmnopqr:abcdef`\), or just by identifier \(i.e., `defghijklmnopqr`\). The latter can be useful when you want to refer to the latest version of a dataset, perhaps produced in a preceding step or workflow.

### Some GitHub action syntax is not supported

Gradient Workflows overlap with GitHub Actions, but are not a duplicate of them. We do some things it doesn't do, such as parallel processing, but not all of its keywords are supported by us, so for example, using `name:` will fail.

### Linting

YAML is flexible with its syntax in some ways, for example comments can be placed somewhat freely. But some arrangements don't work and will cause a syntax error, often manifesting as some other error like`Failed to fetch data`. If desired, you can check that their YAML is valid in a syntactical sense by using one of several YAML checkers online, for example [https://yamllint.com](https://yamllint.com) , before expending compute resources on running a job.

### Commands that contain YAML special characters

Supplying commands in YAML can sometimes conflict with what it deems to be special characters, for example this command to download a large file using curl:

```yaml
- curl
- -C
- -
- -o
- /outputs/catImageDatabase/cat.zip
- 'http://dl.yf.io/lsun/objects/cat.zip'
```

will fail with `Failed to fetch data: "spec.jobs.TestCommands.with.args[2]" must be a string`, because the dash is misread. \(`curl -C - -o` means resume downloading a file after an interrupted network connection.\) In these cases, enclosing the character in quotes can help, and this form succeeds:

```yaml
- curl
- -C
- "-"
- -o
- /outputs/catImageDatabase/cat.zip
- 'http://dl.yf.io/lsun/objects/cat.zip'
```

In this case one could also use the `|-` script syntax as detailed above.

### Command multiple arguments

Often when constructing a workflow you may want to sanity check what is being done, e.g., what files are where. But this can hit YAML syntax too: `ls -a` will work, but `ls -al` will fail with `TypeError: Object of type bytes is not JSON serializable`. As with special characters, a solution is to use the script formulation, so not

```yaml
- bash
- -c
- ls -al
```

but

```yaml
- bash
- -c
- |-
  ls "-al"
```

### Creating and referring to a dataset in the same workflow

An example of information implicit in the workflow that might not match intuition is that there is a difference between referring to a dataset under `inputs: type: dataset`, and the dot syntax. So if one populates the dataset `dstoyg0pdyysyxj` for the first time in the current workflow \(modulo the GUI creation step above to get the ID\), and then tries to refer to it with

```yaml
inputs:
  repo: CloneStyleGAN2Repo.outputs.repo
  catImagesUnzipped:
    type: dataset
    with:
      id: "dstoyg0pdyysyxj"
```

the run will fail because it is expecting an input from outside the workflow, which in this case does not exist. However, using the dot syntax

```yaml
inputs:
  repo: CloneStyleGAN2Repo.outputs.repo
  catImagesUnzipped: UnzipFile.outputs.catImagesUnzipped
```

does work, because now it means the instance of cat images created by the earlier job that contained

```yaml
outputs:
  catImagesUnzipped:
    type: dataset
    with:
      id: "dstoyg0pdyysyxj"
```

\(the dots correspond to indentation\), which, assuming that job succeeded, does exist.

### Datasets are _versioned_

So if you run one workflow and output to a dataset ID:

```yaml
GetCatImageDatabase:
  outputs:
    catImageDatabase:
      type: dataset
      with:
        id: "dsr5spfj2aqzlfg"
```

then run another workflow and output a different file to the same ID, say the MD5 sum of the data instead of the data itself, then it won't update the earlier dataset and add the new file alongside the first one, but create a copy under a new `id:version` that does not contain the previous file. This is the behavior you want for a production setup, with things versioned and often immutable, but it requires remembering how it is working when setting things up.
