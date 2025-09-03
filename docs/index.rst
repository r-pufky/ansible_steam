.. _service-steam:

Steam
#####
Steam dedicated servers with steamcmd and wine.

.. toctree::
  :hidden:
  :maxdepth: -1

  troubleshooting

.. role:: steam
  :service_doc: https://developer.valvesoftware.com/wiki/SteamCMD
  :blocking:    Require upstream repo update, wine dependency updates.
  :private:
  :update:      2022-10-10
  :open:

  Role handles configuration of all provided games servers. See each game for
  game-specific documentation.

Defaults
********
.. literalinclude:: ../defaults/main/main.yml
