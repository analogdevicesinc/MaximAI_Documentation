# Manifold

Manifold is a model-agnostic visual debugging tool for machine learning. Manifold can compare models, detects which subset of data a model is inaccurately predicting, and explains the potential cause of poor model performance by surfacing the feature distribution difference between better and worse-performing subsets of data.

There is a hosted version of Manifold at [http://manifold.mlvis.io/]. To install it locally (for IP reasons and/or higher speed):

On macOS:

```shell
brew install yarn npm
```

On Linux (administrator access is required):

```shell
$ cd $AI_PROJECT_ROOT
$ curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
$ echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
$ curl -sL https://deb.nodesource.com/setup_13.x | sudo -E bash -
$ sudo apt-get update
$ sudo apt-get install -y nodejs yarn
```

On both Mac and Linux:

```shell
$ git clone https://github.com/uber/manifold.git
$ cd manifold
$ yarn
# ignore warnings
$ cd examples/manifold
$ yarn
# ignore warnings
npm run start
```

The actual code will run in JavaScript inside the browser (this may cause warnings that the web page is consuming lots of resources).

## Integration into PyTorch / MAX78000 Training Code

Manifold support can be added to the MAX78000 [training software](https://github.com/MaximIntegratedAI/ai8x-training).

The easiest integration of Manifold is by generating three CSV files during/after training and load them into the demo application (started with the `npm run start` command shown above).

Simple CSV support is built into the training software.

***Note that performance will suffer when there are more than about 20,000 records in the CSV file.*** Subsampling the data is one way to avoid this problem.

## Visualization

The `train.py` program can create CSV files using the `--save-csv` command line argument in combination with `--evaluate`:

```shell
./train.py --model ai85net5 --dataset MNIST --confusion --evaluate --save-csv mnist --device MAX78000 --exp-load-weights-from ../ai8x-synthesis/trained/ai85-mnist.pth.tar -8
```

To run the manifold example application:

```shell
$ cd manifold/examples/manifold
$ npm run start
```

The code will run in JavaScript inside the browser (this may cause warnings that the web page is consuming lots of resources). To run a browser remotely on a development machine, forward X11 using the following command:

```shell
$ ssh -Yn targethost firefox http://localhost:8080/
```

To forward only the remote web port, use `ssh`:

```shell
$ ssh -L 8080:127.0.0.1:8080 targethost
```

## Saving Custom Data

When the built-in `--save-csv` option is insufficient, `train.py` must be modified. The following code snippets demonstrate how to create custom CSV files, open the files and put the field name(s) into the first line.

A batch tensor can be saved to a CSV file using

```python
def save_tensor(t, f):
    """ Save tensor `t` to file handle `f` in CSV format """
    np.savetxt(f, t.reshape(t.shape[0], t.shape[1], -1).mean(axis=2).cpu().numpy(), delimiter=",")
```

This example assumes that the shape of the tensor is (batch_size, features, [feature dimensions]) and averages each feature individually.

The three file handles are opened as follows:

```python
  print('Saving x/ypred/ytrue to CSV...')
  f_ytrue = open('ytrue.csv', 'w')
  f_ytrue.write('hr\n')
  f_ypred = open('ypred.csv', 'w')
  f_ypred.write('hr\n')
  f_x = open('x.csv', 'w')
  f_x.write(','.join(data_fields) + '\n')
```

Then, where appropriate during test, save features/predictions/truth values to CSV:

```python
  save_tensor(local_batch_val, f_x)
  save_tensor(outputs, f_ypred)
  save_tensor(local_label_val, f_ytrue)
```

Finally, close the files:

```python
  f_ytrue.close()
  f_ypred.close()
  f_x.close()
```

