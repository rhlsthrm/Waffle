.. _testing:

Basic testing
=============

Creating a provider
-------------------

Creating a mock provider for your tests is super simple.

.. code-block:: ts

  import { MockProvider } from 'ethereum-waffle';
  const provider = new MockProvider();

This class takes an optional :code:`MockProviderOptions` parameter in the constructor. Then the :code:`ganacheOptions` from :code:`MockProviderOptions` are passed to the underlying ganache-core implementation. You can read more `about the options here <https://github.com/trufflesuite/ganache-core#options>`__.

.. note::
  Prior to Waffle :code:`2.3.0` provider was created using :code:`createMockProvider(options?)`. It behaved exactly like :code:`new MockProvider` but it returned :code:`providers.Web3Provider`, which is the parent class of :code:`MockProvider`.

Getting wallets
---------------

To obtain wallets that have been prefunded with eth use the provider

.. code-block:: ts

  import { MockProvider } from 'ethereum-waffle';

  const provider = new MockProvider();
  const [wallet, otherWallet] = provider.getWallets();

  // or use a shorthand

  const [wallet, otherWallet] = new MockProvider().getWallets();

By default this method returns 10 wallets. You can modify the returned wallets, by changing MockProvider configuration.

.. code-block:: ts

  import { MockProvider } from 'ethereum-waffle';
  const provider = new MockProvider({
      ganacheOptions: {
        accounts: [{balance: 'BALANCE IN WEI', secretKey: 'PRIVATE KEY'}]
      }
  });
  const wallets = provider.getWallets();

You can also get an empty random wallet by calling:

.. code-block:: ts

  import { MockProvider } from 'ethereum-waffle';
  const provider = new MockProvider();
  const wallet = provider.createEmptyWallet();

.. note::
  Prior to Waffle :code:`2.3.0` wallets were obtained using :code:`getWallets(provider)`.

Setup ENS
---------

To setup basic ENS use the provider's function :code:`setupENS()`.

.. code-block:: ts

  import {MockProvider} from '@ethereum-waffle/provider';

  const provider = new MockProvider();
  await provider.setupENS();

This method employs the last of the provider's :code:`wallets` by default, but you can pass your own :code:`wallet` as an argument for :code:`setupENS(wallet)`.

Also :code:`setupENS()` method saves ENS address in the provider's networks.

Read more about ENS functions here :ref:`ens`.

Deploying contracts
-------------------

Once you compile your contracts using waffle you can deploy them in your javascript code. It accepts three arguments:
  - wallet to send the deploy transaction
  - contract information (abi and bytecode)
  - contract constructor arguments

Deploy a contract:

.. code-block:: ts

  import BasicTokenMock from "build/BasicTokenMock.json";

  token = await deployContract(wallet, BasicTokenMock, [wallet.address, 1000]);

The contract information can be one of the following formats:

.. code-block:: ts

  interface StandardContractJSON {
    abi: any;
    evm: {bytecode: {object: any}};
  }

  interface SimpleContractJSON {
    abi: any[];
    bytecode: string;
  }

Linking
-------

Link a library:

.. code-block:: ts

  myLibrary = await deployContract(wallet, MyLibrary, []);
  link(LibraryConsumer, 'contracts/MyLibrary.sol:MyLibrary', myLibrary.address);
  libraryConsumer = await deployContract(wallet, LibraryConsumer, []);

Note: Note: As the second parameter of the link function, you need to use a fully qualified name (path to the file relative to the root of the project, followed by a colon and the contract name).

