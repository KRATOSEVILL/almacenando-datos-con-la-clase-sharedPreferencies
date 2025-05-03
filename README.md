XML
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="24dp"
    android:layout_gravity="center_horizontal">

    <EditText
        android:id="@+id/txtNombre"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/nombre" />

    <EditText
        android:id="@+id/txtTelefono"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/telefono" />

    <Button
        android:id="@+id/btGuardar"
        android:onClick="guardar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/guardar"
        android:textSize="27dp"
        android:layout_gravity="center_horizontal" />

    <Button
        android:id="@+id/btBuscar"
        android:onClick="Buscar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/buscar"
        android:textSize="27dp"
        android:layout_gravity="center_horizontal" />
</LinearLayout>
/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
JAVA
package com.example.lf_app5;

import android.os.Bundle;
import android.content.Context;
import android.content.SharedPreferences;
import android.view.View;
import android.widget.EditText;
import android.widget.Toast;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

public class MainActivity extends AppCompatActivity {
    EditText txtnombre, txttelefono;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);

        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });

        txtnombre = findViewById(R.id.txtNombre);
        txttelefono = findViewById(R.id.txtTelefono);
    }

    public void guardar(View v) {
        String nombre = txtnombre.getText().toString().trim();
        String telefono = txttelefono.getText().toString().trim();

        if (nombre.isEmpty()) {
            Toast.makeText(this, "El nombre no puede ir vacío", Toast.LENGTH_SHORT).show();
            return;
        }
        if (telefono.isEmpty()) {
            Toast.makeText(this, "El teléfono no puede ir vacío", Toast.LENGTH_SHORT).show();
            return;
        }

        SharedPreferences preferences = getSharedPreferences("contactos", Context.MODE_PRIVATE);

        if (preferences.contains(nombre)) {
            Toast.makeText(this, "Ese nombre ya está registrado", Toast.LENGTH_SHORT).show();
            return;
        }

        SharedPreferences.Editor agenda = preferences.edit();
        agenda.putString(nombre, telefono);
        agenda.apply();

        Toast.makeText(this, "Registro guardado", Toast.LENGTH_SHORT).show();
        txtnombre.setText("");
        txttelefono.setText("");
    }

    public void Buscar(View v) {
        SharedPreferences preferences = getSharedPreferences("contactos", Context.MODE_PRIVATE);

        String nombreBuscado = txtnombre.getText().toString().trim();
        String telefonoBuscado = txttelefono.getText().toString().trim();

        if (nombreBuscado.isEmpty() && telefonoBuscado.isEmpty()) {
            Toast.makeText(this, "Debe ingresar un nombre o un teléfono", Toast.LENGTH_SHORT).show();
            return;
        }

    
        if (!nombreBuscado.isEmpty()) {
            String telefono = preferences.getString(nombreBuscado, null);
            if (telefono != null) {
                txttelefono.setText(telefono);
                Toast.makeText(this, "Contacto encontrado por nombre", Toast.LENGTH_SHORT).show();
                return;
            }
        }

 
        if (!telefonoBuscado.isEmpty()) {
            for (String clave : preferences.getAll().keySet()) {
                String telefono = preferences.getString(clave, null);
                if (telefonoBuscado.equals(telefono)) {
                    txtnombre.setText(clave);
                    Toast.makeText(this, "Contacto encontrado por teléfono", Toast.LENGTH_SHORT).show();
                    return;
                }
            }
        }

        Toast.makeText(this, "Contacto no encontrado", Toast.LENGTH_SHORT).show();
    }
}
