.. _supported ID documents: http://onfido.com/supported-documents
.. _Discord: https://discord.gg/xWmQ5tp

.. _troubleshooting-and-known-issues:

================================
Troubleshooting and known issues
================================

.. contents::
   :local:
   :backlinks: none

If you cannot find your issue here, please email testnet@concordium.com,
including logs from the ``concordium-node-retrieve-logs`` tool.

Multiple nodes, bakers, and accounts
====================================

We do not currently support running two nodes on the same machine. When the
``concordium-node`` tool is run, while another node is already running, it will ask you
if you want to stop the currently running node and start a new one.


A node can only run as one baker. A baker is by definition a member of the
committee. The current committee can be obtained by querying the node via

.. code-block:: console

   $concordium-client consensus show-parameters --include-bakers

A node can run as a baker or not, depending on how it is started, and whether the keys it
starts with are registered in the committee.

Which baker the node runs as is determined by the baker keys that it starts with
(these are in the file ``baker-credentials.json``). If a node is restarted it
reloads the keys that exist at the time it starts.

Appearance of high memory consumption of the node
=================================================

The Concordium node may appear to use a lot of memory, and its memory
consumption may grow over time. This happens because the node internally uses
the LMDB database for block and transaction storage. LMDB manages the database
as a single memory-mapped file which can cause certain tools to erroneously
include the whole or parts of the database file into the reported memory
consumption of the process.

Node does not catch up with chain on Windows
============================================

On Windows, the Concordium node may fail to catch up with the chain when
changing internet connections. It usually catches up with the chain again after
few minutes. Otherwise, stop and restart the node.

Failure to catch-up on Windows might also occur, if the time in the docker
virtual machine is out of sync. This can happen as a result of the machine
entering sleep (even if the node is not running at the time). The following may
be indications that this has occurred:

-  Errors in the log such as
   ``ERROR: External: Error importing block: ResultEarlyBlock`` (or any
   other error involving ``ResultEarlyBlock``).
-  Timestamps of messages in the log do not match the system time.
   (Note, the log timestamps are given in the UTC timezone; you may need
   to add or subtract a few hours to convert to your local time.)

To fix this, shutdown the node, restart your computer (or just restart Docker
Desktop), and then restart the node.

If the problem persists, it could be that the system time is set incorrectly.
(This is especially likely if you observe negative times on the Concordium
Network Dashboard.) To fix this, open *Date & time settings* and select *Sync
now* under *Synchronize your clock*. You may have to restart the node as
described above after synchronizing your clock.

(Note: it may be possible to resynchronize the virtual machine's clock without
having to restart it. This can be done by stopping and restarting the *Time
synchronization* integration service for the docker virtual machine using
*Hyper-V Manager*.)

Docker error port already in use
================================

The node may fail starting with the error ``Error starting {..} proxy: listen
tcp 0.0.0.0:{PORT}: bind: address already in use``, which can be remedied by
using a different ``{PORT}`` than the default used by the node.

The ports used by the node are as follows:

-  8888, the port for P2P networking, which can be set with
   ``--listen-node-port``
-  8082, the port used by internal backend, which can be set with
   ``--listen-middleware-port``
-  10000, the gRPC port, which can be set with ``--listen-grpc-port``

When changing the mappings above the docker container must be stopped, reset,
and started again. To reset the container either use ``concordiumn-node-reset-data`` or run the following in a terminal

.. code-block:: console

   $docker rm concordium-client

Special language characters in the Windows client
=================================================

Depending on the code page of the Windows machine, the ``concordium-client`` might not print all special language characters in account names correctly. Instead it replaces unknown characters with a question mark. In such a case, you can either use the account address for client commands instead of the incomplete name, check the name mapping file or change the code page of the terminal to utf-8 by running ``chcp 65001``.

Supported ID document types and countries
=========================================

For issuing a real-world identity in the mobile wallet ``Concordium ID``, i.e.
an identity based on a physical ID document, 3rd parties identity issuer
Notabene and identity verifier Onfido are invoked. For a list of supported ID
document types and countries, please visit `supported ID documents`_.

The previously described identity issuing option can be selected in the process
of creating a new identity using ``Notabene`` on the New Identity Verification
screen. Note that with option ``Notabene development`` test identities can be
issued, which are not verified against physical ID documents.

Inconsistency with numbers on ID documents
==========================================

When issuing an identity in the mobile wallet ``Concordium ID`` with identity provider ``Notabene``, which is based on a Danish ID document or a Spanish national identity card, the personal identity number might be included as document number in the issued identity instead of the actual ID document number.

Danish ID documents (e.g. passport or driver's license) have a document number field and a personal code number field for the CPR number. The number from the document number field should be included as document number in the issued identity. Inconsistencies may be observed where the CPR number is used instead.

The issues are reported to identity provider Notabene as well as identity verifier Onfido.

Performance issues in mobile wallet on Android phones
=====================================================

Performance issues, e.g. long processing times, might occur when running the
mobile wallet ``Concordium ID`` on a low end Android phone. Processing delays
might not be indicated by appropriate waiting screens.

Robustness issues with shielded transfer in mobile wallets
==========================================================

When executing multiple shielded transfers or unshielding flows in the mobile
wallets within a very short period of time, inconsistencies might be observed.
Those robustness issues can be circumvented by waiting a few seconds between
such transactions.

Support & Feedback
==================

If you run into any issues or have suggestions, post your question or feedback
on `Discord`_ or contact us at testnet@concordium.com.
