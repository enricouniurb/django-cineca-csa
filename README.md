Cineca CSA
----------

#### Oracle dblink setup
Scarica il client Oracle e installalo come utente root:
````
unzip SETUP/ORACLE_Setup/instantclient-basic-linux.x64-12.2.0.1.0.zip -d /opt/oracle
echo "/opt/oracle/instantclient_12_2" > /etc/ld.so.conf.d/oracle-instantclient1.conf
ldconfig

````
Per personalizzare le colonne delle tabelle/viste provenienti da CINECA CSA
bisogna ereditare l'app csa o modificarne il contenuto in csa.models.py.

I dati da CSA possono essere usati in due modi, lettura diretta (con cache)
oppure copia dati su tabelle locali. Consultare django_peo/settings.py
per gli esempi di configurazione.

#### Nota su CINECA CSA

Il sistema PEO non dipende da CINECA CSA ma usa questo per allineare i dati.
Questo significa che puoi allineare/creare/gestire i tuoi dati come meglio preferisci.
Ti conviene mantenere la struttura del modello dati così come esposto in csa/models.py
per evitare di dover apporre modifiche.

Se le colonne del tuo DB dovessero chiamarsi diversamente rispetto a CSA, puoi modificare il
mapping in csa/models.py, modifica solo i valori e non le chiavi per garantire il funzionamento del codice:

````
CARRIERA_FIELDS_MAP = {'descr_aff_org': 'ds_aff_org',
                       'descr_sede':    'ds_sede',
                       'descr_inquadramento': 'ds_inquadr',
                       'descr_profilo':       'ds_profilo',
                       'attivita':      'attivita',
                       'data_inizio_rapporto': 'dt_rap_ini',
                       'data_inizio':   'dt_ini',
                       'data_fine':     'dt_fin',
                       'inquadramento': 'inquadr',
                       'ruolo': 'ruolo'}

# per i docenti invence rimuoviamo gli attributi inutili e aggiungiamo quelli specifici
CARRIERA_DOCENTE_FIELDS_MAP = CARRIERA_FIELDS_MAP.copy()
del CARRIERA_DOCENTE_FIELDS_MAP['descr_profilo']
CARRIERA_DOCENTE_FIELDS_MAP.update({"aff_org": "aff_org",
                                    "ds_ruolo": "ds_ruolo",
                                    "ds_attivita": "ds_attivita",
                                    "dt_avanz" : "dt_avanz",
                                    "dt_prox_avanz" : "dt_prox_avanz",
                                    "cd_sett_concors" : "cd_sett_concors",
                                    "ds_sett_concors" : "ds_sett_concors",
                                    "cd_ssd" : "cd_ssd",
                                    "ds_ssd" : "ds_ssd",
                                    "area_ssd" : "area_ssd",
                                    "ds_area_ssd" : "ds_area_ssd",
                                    "scatti" : "scatti",
                                    "inquadramento": "inquadr",
                                    "descr_inquadramento": "ds_inquadr"})

INCARICHI_FIELDS_MAP = {'data_doc': 'data_doc',
                        'num_doc': 'num_doc',
                        'tipo_doc': 'tipo_doc',
                        'descr_tipo': 'des_tipo',
                        'data_inizio':   'dt_ini',
                        'data_fine':     'dt_fin',
                        #'relaz_accomp': 'relaz_accomp',
                        'ruolo': 'ruolo'}
````
